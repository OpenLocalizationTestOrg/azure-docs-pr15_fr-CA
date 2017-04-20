<properties
   pageTitle="Utiliser des données lac banque Java SDK pour développer des applications | Microsoft Azure"
   description="Permet de développer des applications Azure données lac banque Java SDK"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/17/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-java"></a>Mise en route de magasin de LAC de données Azure à l’aide de Java

> [AZURE.SELECTOR]
- [Portail](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [KIT DE DÉVELOPPEMENT .NET](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [API REST](data-lake-store-get-started-rest-api.md)
- [CLI Azure](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

Apprenez à utiliser le SDK Java de banque Azure Data LAC pour effectuer des opérations de base telles que la création de dossiers, charger et téléchargement des fichiers de données, etc.. Pour plus d’informations sur les données lac, consultez [Le lac Azure Data Store](data-lake-store-overview.md).

Vous pouvez accéder à la documentation API du Kit de développement logiciel Java pour Azure données lac banque à [l’API Java de Azure Data lac banque de documents](https://azure.github.io/azure-data-lake-store-java/javadoc/).

## <a name="prerequisites"></a>Conditions préalables

* Kit de développement Java (JDK 7 ou supérieur, à l’aide de Java version 1.7 ou ultérieure)
* Compte de banque de LAC de données Azure. Suivez les instructions de la section [mise en route de magasin de LAC de données Azure via le portail d’Azure](data-lake-store-get-started-portal.md).
* [Maven](https://maven.apache.org/install.html). Ce didacticiel utilise Maven pour les dépendances de génération et de projet. Bien qu’il soit possible de créer sans utiliser un système de génération comme Maven ou Gradle, ces systèmes de création est beaucoup plus facile de gérer les dépendances.
* (Facultatif) Et IDE comme [IntelliJ idée](https://www.jetbrains.com/idea/download/) ou [Eclipse](https://www.eclipse.org/downloads/) ou similaires.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Comment s’authentifient à l’aide d’Azure Active Directory ?

Dans ce didacticiel, nous utilisons un secret de client d’application AD Azure permet de récupérer un jeton Azure Active Directory (service-service d’authentification). Ce jeton nous permet de créer un objet de client de magasin de données LAC pour effectuer des opérations de fichier et de répertoire des opérations. Pour obtenir des instructions sur la manière de s’authentifier avec le magasin de LAC de données Azure en utilisant le mot de passe du client, nous effectuer les étapes suivantes :

1. Créer une application web de publicité Azure
2. Récupérer l’ID de client, client secret et jeton point de terminaison d’application web AD Azure.
3. Configurer l’accès pour l’application de web AD Azure dans le magasin de données lac fichier/dossier que vous souhaitez accéder à partir de l’application Java que vous créez.

Pour obtenir des instructions sur la façon d’effectuer ces étapes, voir [Création d’une application d’Active Directory](data-lake-store-authenticate-using-active-directory.md#create-an-active-directory-application).

Azure Active Directory fournit les autres options pour récupérer un jeton. Vous pouvez choisir à partir d’un certain nombre de mécanismes d’authentification différents en fonction de votre scénario, par exemple, une application s’exécutant dans un navigateur, d’une application distribuée sous la forme d’une application de bureau ou d’une application serveur en cours d’exécution sur site ou dans une machine virtuelle Azure. Vous pouvez également choisir à partir de différents types d’informations d’identification telles que les mots de passe, certificats, 2-authentification, etc.. En outre, Azure Active Directory vous permet de synchroniser vos utilisateurs d’Active Directory sur site avec le nuage. Pour plus d’informations, consultez [Scénarios d’authentification pour Azure Active Directory](../active-directory/active-directory-authentication-scenarios.md). 

## <a name="create-a-java-application"></a>Créer une application Java

Le code exemple disponible [sur GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) parcours vous guide dans le processus de création de fichiers dans le magasin, concaténation de fichiers, téléchargement d’un fichier et la suppression de certains fichiers dans le magasin. Cette section de l’article vous guidons à travers les principales parties du code.

1. Créez un projet Maven utilisant l' [archétype de mvn](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) à partir de la ligne de commande ou à l’aide d’un IDE. Pour obtenir des instructions sur la création d’un projet de Java à l’aide de IntelliJ, consultez [ici](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Pour obtenir des instructions sur la création d’un projet à l’aide d’Eclipse, consultez [ici](http://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm). 

2. Ajouter les dépendances suivantes à votre fichier de **pom.xml** de Maven. Ajouter l’extrait suivant du texte entre la ** \</version >** tag et la ** \</projet >** balise :

        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.0.4-SNAPSHOT</version>
          </dependency>
          <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>1.7.21</version>
          </dependency>
        </dependencies>

    La première dépendance est d’utiliser le SDK de magasin de données Lake (`azure-datalake-store`) à partir du référentiel maven. La deuxième dépendance (`slf4j-nop`) consiste à spécifier la structure de connexion à utiliser pour cette application. Le SDK de magasin de données lac utilise façade [slf4j](http://www.slf4j.org/) enregistrement, ce qui vous permet de choisir parmi un certain nombre d’infrastructures de connexion les plus courants, comme log4j, Java, journalisation, logback, etc., ou aucun enregistrement dans le journal. Pour cet exemple, nous avons désactiver la journalisation, nous utilisons donc la liaison de **slf4j-nop** . Pour utiliser d’autres options d’enregistrement dans votre application, consultez [ici](http://www.slf4j.org/manual.html#projectDep).

### <a name="add-the-application-code"></a>Ajoutez le code de l’application

Il existe trois parties principales du code.

1. Obtenir le jeton d’Azure Active Directory

2. Utiliser le jeton pour créer un client de la banque de données lac.

3. Utiliser le client de la banque de données LAC pour effectuer des opérations.

#### <a name="step-1-obtain-an-azure-active-directory-token"></a>Étape 1 : Obtenir un jeton d’Azure Active Directory.

Le SDK de magasin de données lac propose des méthodes utiles qui vous permettent d’obtenir les jetons de sécurité nécessaires pour communiquer avec le compte de la banque de données lac. Toutefois, le Kit de développement logiciel n’impose pas que seules ces méthodes se servir. Vous pouvez utiliser tout autre moyen d’obtenir le jeton, comme à l’aide du [Kit de développement Azure Active Directory](https://github.com/AzureAD/azure-activedirectory-library-for-java), ou votre propre code personnalisé.

Pour utiliser le SDK de magasin de données LAC pour obtenir le jeton pour l’application Web de Active Directory que vous avez créé précédemment, utilisez les méthodes statiques dans `AzureADAuthenticator` classe. Remplacer le **Remplissage en-ici** avec les valeurs réelles de l’application Azure Active Directory Web.

    private static String clientId = "FILL-IN-HERE";
    private static String authTokenEndpoint = "FILL-IN-HERE";
    private static String clientKey = "FILL-IN-HERE";

    AzureADToken token = AzureADAuthenticator.getTokenUsingClientCreds(authTokenEndpoint, clientId, clientKey);

#### <a name="step-2-create-an-azure-data-lake-store-client-adlstoreclient-object"></a>Étape 2 : Créer un objet de banque de LAC de données Azure de client (ADLStoreClient)

Création d’un objet [ADLStoreClient](https://azure.github.io/azure-data-lake-store-java/javadoc/) vous demande de spécifier le nom de compte de banque de données LAC et le jeton d’Azure Active Directory que vous avez généré dans la dernière étape. Notez que le nom de compte de banque de données lac doit être un nom de domaine pleinement qualifié. Par exemple, remplacer le **Remplissage en-ici** quelque chose comme **mydatalakestore.azuredatalakestore.net**.

    private static String accountFQDN = "FILL-IN-HERE";  // full account FQDN, not just the account name
    ADLStoreClient client = ADLStoreClient.createClient(accountFQDN, token);

### <a name="step-3-use-the-adlstoreclient-to-perform-file-and-directory-operations"></a>Étape 3 : Utiliser le ADLStoreClient pour effectuer des opérations de fichier et de répertoire

Le code ci-dessous contient des extraits d’exemples d’opérations courantes. Vous pouvez consulter la complète [données lac banque Java SDK API docs](https://azure.github.io/azure-data-lake-store-java/javadoc/) de l’objet **ADLStoreClient** pour voir d’autres opérations.
 
Notez que les fichiers sont lues et écrites dans à l’aide de flux de données Java standard. Cela signifie que vous pouvez superposer des flux de données Java sur les flux de données de magasin de données LAC pour bénéficier de fonctionnalités Java standard (par exemple, imprimer flux de données pour la sortie mise en forme, ou l’un des flux de compression ou de cryptage pour des fonctionnalités supplémentaires sur le haut, etc..).

    // set file permission
    client.setPermission(filename, "744");

    // append to file
    stream = client.getAppendStream(filename);
    stream.write(getSampleContent());
    stream.close();

    // Read File
    InputStream in = client.getReadStream(filename);
    byte[] b = new byte[64000];
    while (in.read(b) != -1) {
        System.out.write(b);
    }
    in.close();

    // concatenate the two files into one
    List<String> fileList = Arrays.asList("/a/b/c.txt", "/a/b/d.txt");
    client.concatenateFiles("/a/b/f.txt", fileList);

    //rename the file
    client.rename("/a/b/f.txt", "/a/b/g.txt");

    // list directory contents
    List<DirectoryEntry> list = client.enumerateDirectory("/a/b", 2000);
    System.out.println("Directory listing for directory /a/b:");
    for (DirectoryEntry entry : list) {
        printDirectoryInfo(entry);
    }

    // delete directory along with all the subdirectories and files in it
    client.deleteRecursive("/a");

#### <a name="step-4-build-and-run-the-application"></a>Étape 4 : Créer et exécuter l’application

1. Pour exécuter à partir d’une interface IDE, localisez et appuyez sur le bouton **exécuter** . Pour exécuter Maven, utilisez [exec : exec](http://www.mojohaus.org/exec-maven-plugin/exec-mojo.html).

2. Pour produire un fichier jar autonome que vous pouvez exécuter depuis la ligne de commande Générer le fichier jar avec toutes les dépendances sont incluses, en utilisant le [plug-in d’assembly Maven](http://maven.apache.org/plugins/maven-assembly-plugin/usage.html). Le pom.xml dans l' [exemple de code source sur github](https://github.com/Azure-Samples/data-lake-store-java-upload-download-get-started/blob/master/pom.xml) contient un exemple de cette procédure.


## <a name="next-steps"></a>Étapes suivantes

- [Sécuriser les données dans le magasin de données lac](data-lake-store-secure-data.md)
- [Utiliser des données Azure lac Analytique avec le magasin de données lac](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Utilisez HDInsight Azure avec le magasin de données lac](data-lake-store-hdinsight-hadoop-use-portal.md)
