<properties
    pageTitle="Application Java de calcul intensif sur une machine virtuelle | Microsoft Azure"
    description="Apprenez à créer un ordinateur virtuel Azure qui exécute une application Java de calcul intensif qui peut être analysée par une autre application Java."
    services="virtual-machines-windows"
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""
    tags="azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="how-to-run-a-compute-intensive-task-in-java-on-a-virtual-machine"></a>Comment faire pour exécuter une tâche de calcul intensif dans Java sur un ordinateur virtuel

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]
 

Avec Azure, vous pouvez utiliser un ordinateur virtuel pour gérer les tâches de calcul intensif. Par exemple, une machine virtuelle peut gérer des tâches et fournir des résultats aux ordinateurs clients ou des applications mobiles. Après avoir lu cet article, vous devez comprendre comment créer un ordinateur virtuel qui exécute une application Java de calcul intensif qui peut être analysée par une autre application Java.

Ce didacticiel suppose que vous savez comment créer des applications de console Java pouvez importer les bibliothèques à votre application Java et pouvez générer une archive Java (JAR). Aucune connaissance de Microsoft Azure n’est supposé.

Vous allez apprendre :

* Comment créer un ordinateur virtuel avec un Java JDK (Development Kit) déjà installé.
* Comment se connecter à distance à votre ordinateur virtuel.
* Comment créer un espace de noms de bus de service.
* Comment créer une application Java qui effectue une tâche de calcul intensif.
* Comment créer une application Java qui surveille la progression de la tâche de calcul intensif.
* Comment faire pour exécuter les applications Java.
* Comment arrêter les applications Java.

Ce didacticiel utilise le problème du voyageur de la tâche de calcul intensif. Voici un exemple de l’application Java en exécutant la tâche de calcul intensif.

![Solveur de problème du voyageur][solver_output]

Voici un exemple de l’application Java contrôle la tâche de calcul intensif.

![Client du problème du voyageur][client_output]

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="to-create-a-virtual-machine"></a>Pour créer un ordinateur virtuel

1. Ouvrez une session [Azure portal classique](https://manage.windowsazure.com).
2. Cliquez sur **Nouveau**et cliquez sur **Calculer**, cliquez sur **ordinateur virtuel**, puis cliquez sur **à partir de la galerie**.
3. Dans la boîte de dialogue **Sélectionner des images de machine virtuelle** , sélectionnez **JDK 7 Windows Server 2012**.
Notez que le **JDK 6 Windows Server 2012** est disponible au cas où vous auriez des applications héritées qui ne sont pas encore prêtes à exécuter dans le JDK 7.
4. Cliquez sur **suivant**.
4. Dans la boîte de dialogue de **configuration de machine virtuelle** :
    1. Spécifiez un nom pour l’ordinateur virtuel.
    2. Spécifiez la taille à utiliser pour la machine virtuelle.
    3. Dans le champ **Nom d’utilisateur** , entrez un nom pour l’administrateur. N’oubliez pas ce nom et le mot de passe que vous entrez ensuite, vous allez l’utiliser lorsque vous vous connectez à distance à l’ordinateur virtuel.
    4. Entrez un mot de passe dans le champ **nouveau mot de passe** , et entrez-le dans le champ **Confirmer** . C’est le mot de passe du compte administrateur.
    5. Cliquez sur **suivant**.
5. Dans la boîte de dialogue **configuration de l’ordinateur virtuel** suivante :
    1. Pour le **service en nuage**, utilisez la valeur par défaut de **créer un nouveau service en nuage**.
    2. La valeur pour le **nom DNS du service nuage** doit être unique au sein de cloudapp.net. Si nécessaire, modifiez cette valeur afin qu’Azure indique qu’il est unique.
    2. Permet de spécifier une région, une affinité du groupe ou réseau virtuel. Pour les besoins de ce didacticiel, spécifier une région, par exemple **US-ouest**.
    2. Pour le **Compte de stockage**, sélectionnez **utiliser un compte de stockage généré automatiquement**.
    3. Pour **Définir de disponibilité**, sélectionnez **(aucun)**.
    4. Cliquez sur **suivant**.
5. Dans la dernière boîte de dialogue de **configuration de machine virtuelle** :
    1. Accepter les entrées de point de terminaison par défaut.
    2. Cliquez sur **terminé**.

## <a name="to-remotely-log-in-to-your-virtual-machine"></a>Pour vous connecter à distance à votre ordinateur virtuel

1. Ouvrez une session sur [Azure portal classique](https://manage.windowsazure.com).
2. Cliquez sur des **machines virtuelles**.
3. Cliquez sur le nom de l’ordinateur virtuel que vous souhaitez connecter à.
4. Cliquez sur **se connecter**.
5. Répondez aux invites comme nécessaire pour se connecter à l’ordinateur virtuel. Lorsque le système demande le nom de l’administrateur et le mot de passe, utilisez les valeurs que vous avez fournies lorsque vous avez créé l’ordinateur virtuel.

Notez que la fonctionnalité de Bus des services Azure requiert le certificat racine de CyberTrust Baltimore pour être installé dans le cadre de la banque d’informations de votre JRE **cacerts** . Ce certificat est automatiquement inclus dans le Java Runtime Environment (JRE) utilisé par ce didacticiel. Si vous ne disposez pas de ce certificat dans votre magasin de **cacerts** JRE, reportez-vous à la section [Ajout d’un certificat dans le magasin de certificats d’autorité de certification Java] [ add_ca_cert] pour plus d’informations sur l’ajout d’elle (ainsi que des informations sur l’affichage des certificats dans votre banque cacerts).

## <a name="how-to-create-a-service-bus-namespace"></a>Comment faire pour créer un espace de noms de bus de service

Pour commencer à utiliser des files d’attente de Bus de Service dans Azure, vous devez d’abord créer un espace de noms du service. Un espace de noms du service fournit un conteneur de portée pour l’adressage des ressources de Bus de Service au sein de votre application.

Pour créer un espace de noms du service :

1.  Ouvrez une session sur [Azure portal classique](https://manage.windowsazure.com).
2.  Dans le volet de navigation inférieure gauche du portail Azure classique, cliquez sur **Bus de Service, de contrôle d’accès et de mise en cache**.
3.  Dans le volet supérieur gauche du portail Azure classique, cliquez sur le nœud de **Bus des services** et puis cliquez sur le bouton **Nouveau** .  
    ![Capture d’écran de nœud de Bus de service][svc_bus_node]
4.  Dans la boîte de dialogue **créer un nouveau Namespace de Service** , entrez un **Namespace**et pour vous assurer qu’il est unique, cliquez sur le bouton **Vérifier la disponibilité** .  
    ![Créer une capture d’écran de nouveau Namespace][create_namespace]
5.  Une fois que le nom de l’espace de noms est disponible, choisissez le pays ou la région dans laquelle votre espace de noms doit être hébergé, puis cliquez sur le bouton **Créer un Namespace** .  

    L’espace de noms que vous avez créé s’affiche alors dans le portail classique Azure et prend quelques minutes pour l’activer. Attendez que l’état est **actif** avant de passer à l’étape suivante.

## <a name="obtain-the-default-management-credentials-for-the-namespace"></a>Obtenir les informations d’identification de gestion par défaut pour l’espace de noms

Pour effectuer des opérations de gestion, telles que la création d’une file d’attente, dans l’espace de noms, vous devez obtenir les informations d’identification de la gestion de l’espace de noms.

1.  Dans le volet de navigation de gauche, cliquez sur le nœud de **Bus des services** pour afficher la liste des espaces de noms disponibles.
    ![Capture d’écran des espaces de noms disponible][avail_namespaces]
2.  Sélectionnez l’espace de noms que vous venez de créer dans la liste affichée.
    ![Capture d’écran de la liste du Namespace][namespace_list]
3.  Le volet **Propriétés** de droite répertorie les propriétés de l’espace de noms.
    ![Capture d’écran du volet Propriétés][properties_pane]
4.  La **Clé de la valeur par défaut** est masquée. Cliquez sur le bouton **Afficher** pour afficher les informations d’identification de sécurité.
    ![Capture d’écran de la clé par défaut][default_key]
5.  Prenez note de **Valeur par défaut de l’émetteur** et la **Clé de la valeur par défaut** que vous utiliserez ces informations ci-dessous pour effectuer des opérations avec l’espace de noms.

## <a name="how-to-create-a-java-application-that-performs-a-compute-intensive-task"></a>Comment créer une application Java qui effectue une tâche de calcul intensif

1. Sur votre ordinateur de développement (qui ne doit pas être la machine virtuelle que vous avez créé), téléchargez le [Azure SDK pour Java](https://azure.microsoft.com/develop/java/).
2. Créez une application de console Java à l’aide de l’exemple de code à la fin de cette section. Dans ce didacticiel, nous allons utiliser **TSPSolver.java** comme nom de fichier Java. Modifier la **votre\_service\_bus\_espace de noms**, **votre\_service\_bus\_propriétaire**, et **votre\_service\_bus\_clé** des espaces réservés à utiliser le service de bus **espace de noms**, **Par défaut l’émetteur** et les valeurs **Par défaut de clé** , respectivement.
3. Après le codage, exporter l’application vers une archive Java exécutable (JAR) et les bibliothèques requises du package dans le fichier JAR généré. Dans ce didacticiel, nous allons utiliser **TSPSolver.jar** comme nom de fichier JAR généré.

<p/>

    // TSPSolver.java

    import com.microsoft.windowsazure.services.core.Configuration;
    import com.microsoft.windowsazure.services.core.ServiceException;
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import java.io.*;
    import java.text.DateFormat;
    import java.text.SimpleDateFormat;
    import java.util.ArrayList;
    import java.util.Date;
    import java.util.List;

    public class TSPSolver {

        //  Value specifying how often to provide an update to the console.
        private static long loopCheck = 100000000;  

        private static long nTimes = 0, nLoops=0;

        private static double[][] distances;
        private static String[] cityNames;
        private static int[] bestOrder;
        private static double minDistance;
        private static ServiceBusContract service;

        private static void buildDistances(String fileLocation, int numCities) throws Exception{
            try{
                BufferedReader file = new BufferedReader(new InputStreamReader(new DataInputStream(new FileInputStream(new File(fileLocation)))));
                double[][] cityLocs = new double[numCities][2];
                for (int i = 0; i<numCities; i++){
                    String[] line = file.readLine().split(", ");
                    cityNames[i] = line[0];
                    cityLocs[i][0] = Double.parseDouble(line[1]);
                    cityLocs[i][1] = Double.parseDouble(line[2]);
                }
                for (int i = 0; i<numCities; i++){
                    for (int j = i; j<numCities; j++){
                        distances[i][j] = Math.hypot(Math.abs(cityLocs[i][0] - cityLocs[j][0]), Math.abs(cityLocs[i][1] - cityLocs[j][1]));
                        distances[j][i] = distances[i][j];
                    }
                }
            } catch (Exception e){
                throw e;
            }
        }

        private static void permutation(List<Integer> startCities, double distSoFar, List<Integer> restCities) throws Exception {

            try
            {
                nTimes++;
                if (nTimes == loopCheck)
                {
                    nLoops++;
                    nTimes = 0;
                    DateFormat dateFormat = new SimpleDateFormat("MM/dd/yyyy HH:mm:ss");
                    Date date = new Date();
                    System.out.print("Current time is " + dateFormat.format(date) + ". ");
                    System.out.println(  "Completed " + nLoops + " iterations of size of " + loopCheck + ".");
                }

                if ((restCities.size() == 1) && ((minDistance == -1) || (distSoFar + distances[restCities.get(0)][startCities.get(0)] + distances[restCities.get(0)][startCities.get(startCities.size()-1)] < minDistance))){
                    startCities.add(restCities.get(0));
                    newBestDistance(startCities, distSoFar + distances[restCities.get(0)][startCities.get(0)] + distances[restCities.get(0)][startCities.get(startCities.size()-2)]);
                    startCities.remove(startCities.size()-1);
                }
                else{
                    for (int i=0; i<restCities.size(); i++){
                        startCities.add(restCities.get(0));
                        restCities.remove(0);
                        permutation(startCities, distSoFar + distances[startCities.get(startCities.size()-1)][startCities.get(startCities.size()-2)],restCities);
                        restCities.add(startCities.get(startCities.size()-1));
                        startCities.remove(startCities.size()-1);
                    }
                }
            }
            catch (Exception e)
            {
                throw e;
            }
        }

        private static void newBestDistance(List<Integer> cities, double distance) throws ServiceException, Exception {
            try
            {
                minDistance = distance;
                String cityList = "Shortest distance is "+minDistance+", with route: ";
                for (int i = 0; i<bestOrder.length; i++){
                    bestOrder[i] = cities.get(i);
                    cityList += cityNames[bestOrder[i]];
                    if (i != bestOrder.length -1)
                        cityList += ", ";
                }
                System.out.println(cityList);
                service.sendQueueMessage("TSPQueue", new BrokeredMessage(cityList));
            }
            catch (ServiceException se)
            {
                throw se;
            }
            catch (Exception e)
            {
                throw e;
            }
        }

        public static void main(String args[]){

            try {

                Configuration config = ServiceBusConfiguration.configureWithWrapAuthentication(
                        "your_service_bus_namespace", "your_service_bus_owner",
                        "your_service_bus_key",
                        ".servicebus.windows.net",
                        "-sb.accesscontrol.windows.net/WRAPv0.9");

                service = ServiceBusService.create(config);

                int numCities = 10;  // Use as the default, if no value is specified at command line.
                if (args.length != 0)
                {
                    if (args[0].toLowerCase().compareTo("createqueue")==0)
                    {
                        // No processing to occur other than creating the queue.
                        QueueInfo queueInfo = new QueueInfo("TSPQueue");

                        service.createQueue(queueInfo);

                        System.out.println("Queue named TSPQueue was created.");

                        System.exit(0);
                    }

                    if (args[0].toLowerCase().compareTo("deletequeue")==0)
                    {
                        // No processing to occur other than deleting the queue.
                        service.deleteQueue("TSPQueue");

                        System.out.println("Queue named TSPQueue was deleted.");

                        System.exit(0);
                    }

                    // Neither creating or deleting a queue.
                    // Assume the value passed in is the number of cities to solve.
                    numCities = Integer.valueOf(args[0]);  
                }

                System.out.println("Running for " + numCities + " cities.");

                List<Integer> startCities = new ArrayList<Integer>();
                List<Integer> restCities = new ArrayList<Integer>();
                startCities.add(0);
                for(int i = 1; i<numCities; i++)
                    restCities.add(i);
                distances = new double[numCities][numCities];
                cityNames = new String[numCities];
                buildDistances("c:\\TSP\\cities.txt", numCities);
                minDistance = -1;
                bestOrder = new int[numCities];
                permutation(startCities, 0, restCities);
                System.out.println("Final solution found!");
                service.sendQueueMessage("TSPQueue", new BrokeredMessage("Complete"));
            }
            catch (ServiceException se)
            {
                System.out.println(se.getMessage());
                se.printStackTrace();
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.println(e.getMessage());
                e.printStackTrace();
                System.exit(-1);
            }
        }

    }



## <a name="how-to-create-a-java-application-that-monitors-the-progress-of-the-compute-intensive-task"></a>Comment créer une application Java qui surveille la progression de la tâche de calcul intensif

1. Sur votre ordinateur de développement, créez une application de console Java à l’aide de l’exemple de code à la fin de cette section. Dans ce didacticiel, nous allons utiliser **TSPClient.java** comme nom de fichier Java. Comme indiqué précédemment, modifiez la **votre\_service\_bus\_espace de noms**, **votre\_service\_bus\_propriétaire**, et **votre\_service\_bus\_clé** des espaces réservés à utiliser le service de bus **espace de noms**, **Par défaut l’émetteur** et les valeurs **Par défaut de clé** , respectivement.
2. Exporter un fichier JAR exécutable de l’application et les bibliothèques requises du package dans le fichier JAR généré. Dans ce didacticiel, nous allons utiliser **TSPClient.jar** comme nom de fichier JAR généré.

<p/>

    // TSPClient.java

    import java.util.Date;
    import java.text.DateFormat;
    import java.text.SimpleDateFormat;
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import com.microsoft.windowsazure.services.core.*;

    public class TSPClient
    {

        public static void main(String[] args)
        {
                try
                {

                    DateFormat dateFormat = new SimpleDateFormat("MM/dd/yyyy HH:mm:ss");
                    Date date = new Date();
                    System.out.println("Starting at " + dateFormat.format(date) + ".");

                    String namespace = "your_service_bus_namespace";
                    String issuer = "your_service_bus_owner";
                    String key = "your_service_bus_key";

                    Configuration config;
                    config = ServiceBusConfiguration.configureWithWrapAuthentication(
                            namespace, issuer, key,
                            ".servicebus.windows.net",
                            "-sb.accesscontrol.windows.net/WRAPv0.9");

                    ServiceBusContract service = ServiceBusService.create(config);

                    BrokeredMessage message;

                    int waitMinutes = 3;  // Use as the default, if no value is specified at command line.
                    if (args.length != 0)
                    {
                        waitMinutes = Integer.valueOf(args[0]);  
                    }

                    String waitString;

                    waitString = (waitMinutes == 1) ? "minute." : waitMinutes + " minutes.";

                    // This queue must have previously been created.
                    service.getQueue("TSPQueue");

                    int numRead;

                    String s = null;

                    while (true)
                    {

                        ReceiveQueueMessageResult resultQM = service.receiveQueueMessage("TSPQueue");
                        message = resultQM.getValue();

                        if (null != message && null != message.getMessageId())
                        {

                            // Display the queue message.
                            byte[] b = new byte[200];

                            System.out.print("From queue: ");

                            s = null;
                            numRead = message.getBody().read(b);
                            while (-1 != numRead)
                            {
                                s = new String(b);
                                s = s.trim();
                                System.out.print(s);
                                numRead = message.getBody().read(b);
                            }
                            System.out.println();
                            if (s.compareTo("Complete") == 0)
                            {
                                // No more processing to occur.
                                date = new Date();
                                System.out.println("Finished at " + dateFormat.format(date) + ".");
                                break;
                            }
                        }
                        else
                        {
                            // The queue is empty.
                            System.out.println("Queue is empty. Sleeping for another " + waitString);
                            Thread.sleep(60000 * waitMinutes);
                        }
                    }

            }
            catch (ServiceException se)
            {
                System.out.println(se.getMessage());
                se.printStackTrace();
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.println(e.getMessage());
                e.printStackTrace();
                System.exit(-1);
            }

        }

    }

## <a name="how-to-run-the-java-applications"></a>Comment faire pour exécuter les applications Java
Exécutez l’application de calcul intensif, commencer par créer la file d’attente, puis pour résoudre le problème en déplacement de Saleseman, qui ajoute le meilleur itinéraire en cours à la file d’attente de bus de service. Alors que les applications de calcul intensif est en cours d’exécution (ou ultérieurement), exécuter le client pour afficher les résultats à partir de la file d’attente de bus de service.

### <a name="to-run-the-compute-intensive-application"></a>Pour exécuter l’application de calcul intensif

1. Ouvrez une session sur votre ordinateur virtuel.
2. Créez un dossier dans lequel vous allez exécuter votre application. Par exemple, **c:\TSP**.
3. Copiez **TSPSolver.jar** à **c:\TSP**,
4. Créez un fichier nommé **c:\TSP\cities.txt** avec le contenu suivant.

        City_1, 1002.81, -1841.35
        City_2, -953.55, -229.6
        City_3, -1363.11, -1027.72
        City_4, -1884.47, -1616.16
        City_5, 1603.08, -1030.03
        City_6, -1555.58, 218.58
        City_7, 578.8, -12.87
        City_8, 1350.76, 77.79
        City_9, 293.36, -1820.01
        City_10, 1883.14, 1637.28
        City_11, -1271.41, -1670.5
        City_12, 1475.99, 225.35
        City_13, 1250.78, 379.98
        City_14, 1305.77, 569.75
        City_15, 230.77, 231.58
        City_16, -822.63, -544.68
        City_17, -817.54, -81.92
        City_18, 303.99, -1823.43
        City_19, 239.95, 1007.91
        City_20, -1302.92, 150.39
        City_21, -116.11, 1933.01
        City_22, 382.64, 835.09
        City_23, -580.28, 1040.04
        City_24, 205.55, -264.23
        City_25, -238.81, -576.48
        City_26, -1722.9, -909.65
        City_27, 445.22, 1427.28
        City_28, 513.17, 1828.72
        City_29, 1750.68, -1668.1
        City_30, 1705.09, -309.35
        City_31, -167.34, 1003.76
        City_32, -1162.85, -1674.33
        City_33, 1490.32, 821.04
        City_34, 1208.32, 1523.3
        City_35, 18.04, 1857.11
        City_36, 1852.46, 1647.75
        City_37, -167.44, -336.39
        City_38, 115.4, 0.2
        City_39, -66.96, 917.73
        City_40, 915.96, 474.1
        City_41, 140.03, 725.22
        City_42, -1582.68, 1608.88
        City_43, -567.51, 1253.83
        City_44, 1956.36, 830.92
        City_45, -233.38, 909.93
        City_46, -1750.45, 1940.76
        City_47, 405.81, 421.84
        City_48, 363.68, 768.21
        City_49, -120.3, -463.13
        City_50, 588.51, 679.33

5. À une invite de commandes, accédez à c:\TSP.
6. Assurez-vous que dossier du JRE bin se trouve dans la variable d’environnement PATH.
7. Vous devez créer la file d’attente de bus de service avant d’exécuter les permutations de solveur TSP. Exécutez la commande suivante pour créer la file d’attente de bus de service.

        java -jar TSPSolver.jar createqueue

8. Maintenant que la file d’attente est créé, vous pouvez exécuter les permutations de solveur TSP. Par exemple, exécutez la commande suivante pour exécuter le solveur pour 8 villes.

        java -jar TSPSolver.jar 8

 Si vous ne spécifiez pas un nombre, il exécutera 10 villes. Lorsque le Solveur trouve des itinéraires les plus courts en cours, il les ajoute à la file d’attente.

> [AZURE.NOTE]
> Plus le nombre que vous spécifiez, plus le Solveur s’exécutera. Par exemple, en cours d’exécution 14 villes peuvent prendre plusieurs minutes et 15 villes en cours d’exécution peut prendre plusieurs heures. Augmentation au villes de 16 ans ou plus, vous risquez de jours du runtime (éventuellement plusieurs semaines, mois et années). C’est en raison de l’augmentation rapide du nombre de permutations évaluée par le solveur, comme le nombre de villes augmente.

### <a name="how-to-run-the-monitoring-client-application"></a>Comment faire pour exécuter l’application cliente de surveillance
1. Ouvrez une session sur votre ordinateur où vous allez exécuter l’application cliente. Cela n’a pas besoin d’être le même ordinateur que celui exécutant l’application **TSPSolver** , bien qu’il puisse être.
2. Créez un dossier dans lequel vous allez exécuter votre application. Par exemple, **c:\TSP**.
3. Copiez **TSPClient.jar** à **c:\TSP**,
4. Assurez-vous que dossier du JRE bin se trouve dans la variable d’environnement PATH.
5. À une invite de commandes, accédez à c:\TSP.
6. Exécutez la commande suivante.

        java -jar TSPClient.jar

    Vous pouvez également spécifier le nombre de minutes entre la vérification de la file d’attente, en passant un argument de ligne de commande de mise en veille. La période de veille par défaut pour la vérification de la file d’attente est de 3 minutes, qui est utilisé si aucun argument de ligne de commande n’est passé à **TSPClient**. Si vous souhaitez utiliser une autre valeur pour l’intervalle de veille, par exemple, une minute, exécutez la commande suivante.

        java -jar TSPClient.jar 1

    Le client s’exécute jusqu'à ce qu’il voie un message de la file d’attente de « Complete ». Notez que si vous exécutez plusieurs occurrences du solveur sans exécuter le client, vous devrez peut-être exécuter le client plusieurs fois entièrement vider la file d’attente. Vous pouvez également supprimer la file d’attente et puis le créer à nouveau. Pour supprimer la file d’attente, exécutez la commande suivante de la **TSPSolver** (pas **TSPClient**).

        java -jar TSPSolver.jar deletequeue

    Le Solveur s’exécute jusqu'à ce qu’il issue de l’examen de toutes les gammes.

## <a name="how-to-stop-the-java-applications"></a>Comment arrêter les applications Java
Pour le solveur et les applications clientes, vous pouvez appuyer sur **Ctrl + C** pour quitter si vous voulez que se termine avant la fin normale.


[solver_output]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/WA_JavaTSPSolver.png
[client_output]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/WA_JavaTSPClient.png
[svc_bus_node]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/SvcBusQueues_02_SvcBusNode.jpg
[create_namespace]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/SvcBusQueues_03_CreateNewSvcNamespace.jpg
[avail_namespaces]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/SvcBusQueues_04_SvcBusNode_AvailNamespaces.jpg
[namespace_list]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/SvcBusQueues_05_NamespaceList.jpg
[properties_pane]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/SvcBusQueues_06_PropertiesPane.jpg
[default_key]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/SvcBusQueues_07_DefaultKey.jpg
[add_ca_cert]: ../java-add-certificate-ca-store.md
