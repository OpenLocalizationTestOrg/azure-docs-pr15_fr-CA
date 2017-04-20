<properties
   pageTitle="Topologies de tempête de Apache avec Visual Studio et C# | Microsoft Azure"
   description="Apprenez à créer des topologies de tempête dans C# par la création d’une topologie de comptage simple word dans Visual Studio à l’aide des outils HDInsight pour Visual Studio."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
   tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/27/2016"
   ms.author="larryfr"/>

# <a name="develop-c-topologies-for-apache-storm-on-hdinsight-using-hadoop-tools-for-visual-studio"></a>Développer des topologies de C# pour tempête Apache sur HDInsight à l’aide des outils d’Hadoop pour Visual Studio

Apprenez à créer une topologie de tempête C# pour Visual Studio à l’aide des outils HDInsight. Ce didacticiel vous guide à travers le processus de création d’un nouveau projet de tempête dans Visual Studio, tester localement et le déploiement d’une vague d’Apache sur cluster de HDInsight.

Vous apprendrez également à créer des topologies hybrides qui utilisent les composants Java et C#.

> [AZURE.IMPORTANT] Tandis que les étapes décrites dans ce document se basent sur un environnement de développement Windows avec Visual Studio, le projet compilé peut être soumis à un cluster d’un HDInsight de fonctionnant sous Windows ou Linux. Ne clusters basés sur Linux créés après le 28/10/2016 prise en charge de topologies SCP.NET.
>
> Pour utiliser une topologie C# avec un cluster Linux, vous devez mettre à jour le package NuGet de Microsoft.SCP.Net.SDK utilisé par votre projet vers la version 0.10.0.6 ou ultérieure. La version du package doit correspondre également à la version principale de la tempête installé sur HDInsight. Par exemple, tempête sur HDInsight les versions 3.3 et 3.4 utiliser tempête 0.10.x, tandis que HDInsight 3.5 utilise tempête 1.0.x.
> 
> Topologies C# sur les clusters basés sur Linux doivent utiliser .NET 4.5 et Mono permet d’exécuter sur le cluster de HDInsight. La plupart des choses fonctionnent, cependant, vous devez vérifier la [Compatibilité de Mono](http://www.mono-project.com/docs/about-mono/compatibility/) document pour identifier les éventuelles incompatibilités.

## <a name="prerequisites"></a>Conditions préalables

- Une des versions suivantes de Visual Studio

    - Visual Studio 2012 avec la [mise à jour 4](http://www.microsoft.com/download/details.aspx?id=39305)

    - Visual Studio 2013 avec la [Communauté de 2013 Visual Studio](http://go.microsoft.com/fwlink/?LinkId=517284) ou de [mise à jour 4](http://www.microsoft.com/download/details.aspx?id=44921)

    - 2015 de Visual Studio ou [Visual Studio communautaire de 2015](https://go.microsoft.com/fwlink/?LinkId=532606)

- Azure SDK 2.9.5 ou version ultérieure

- Outils d’HDInsight pour Visual Studio : reportez-vous à la section [mise en route de HDInsight les outils de Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md) pour installer et configurer les outils de HDInsight pour Visual Studio.

    > [AZURE.NOTE] Outils HDInsight pour Visual Studio ne sont pas prises en charge de Visual Studio Express

-   Tempête de Apache sur HDInsight cluster : reportez-vous à la section [mise en route avec la tempête Apache sur HDInsight](hdinsight-apache-storm-tutorial-get-started.md) pour obtenir la procédure pour créer un cluster.

## <a name="templates"></a>Modèles

Les outils de HDInsight pour Visual Studio fournit les modèles suivants :

| Type de projet | Montre |
| ------------ | ------------- |
| Application de tempête | Un projet de topologie tempête vide |
| Exemple de générateur SQL Azure tempête | Comment faire pour écrire dans la base de données de SQL Azure |
| Exemple de DocumentDB lecteur de tempête | Comment faire pour lire à partir de DocumentDB d’Azure |
| Exemple de générateur de DocumentDB de tempête | Comment écrire dans Azure DocumentDB |
| Exemple de EventHub lecteur de tempête | La lecture de concentrateurs d’événement Azure |
| Exemple de générateur de EventHub de tempête | Comment écrire dans Azure événement concentrateurs |
| Exemple de HBase lecteur de tempête | Comment faire pour lire à partir de HBase sur des clusters de HDInsight |
| Exemple de générateur de HBase de tempête | Comment faire pour écrire sur HBase HDInsight clusters |
| Tempête hybride, exemple | Comment utiliser un composant Java |
| Exemple de tempête | Une topologie de nombre de base word |

> [AZURE.NOTE] Les exemples de lecteur et un writer HBase utilisent l’API REST de HBase pour communiquer avec un HBase sur HDInsight cluster, pas l’API de Java HBase.

Dans les étapes de ce document, vous utiliserez le type de projet d’Application de la tempête base pour créer une nouvelle topologie.

## <a name="create-a-c-topology"></a>Créer une topologie de C#

1. Si vous n’avez pas déjà installé la dernière version des outils HDInsight pour Visual Studio, reportez-vous à la section [mise en route à l’aide des outils HDInsight pour Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).

2. Ouvrez Visual Studio, sélectionnez le **fichier** > **Nouveau**, puis **projet**.

3. Dans l’écran **Nouveau projet** , développez **installé** > **modèles**et sélectionnez **HDInsight**. À partir de la liste des modèles, sélectionnez **Application de tempête**. En bas de l’écran, entrez le nom de l’application **WordCount** .

    ![image](./media/hdinsight-storm-develop-csharp-visual-studio-topology/new-project.png)

4. Une fois que le projet a été créé, vous devez avoir les fichiers suivants :

    - **Program.cs**: définit la topologie de votre projet. Notez qu’une topologie par défaut qui se compose d’un bec et un boulon est créée par défaut.

    - **Spout.cs**: un bec exemple qui émet des nombres aléatoires.

    - **Bolt.cs**: un boulon d’exemple qui conserve un décompte des nombres émis par bec.

    Dans le cadre de la création du projet, les derniers [packages SCP.NET](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/) seront téléchargés à partir de NuGet.

    [AZURE.INCLUDE [scp.net version important](../../includes/hdinsight-storm-scpdotnet-version.md)]

Dans les sections suivantes, vous allez modifier ce projet dans une application de base WordCount.

### <a name="implement-the-spout"></a>Implémenter le bec

1. Ouvrez **Spout.cs**. Becs verseurs sont utilisés pour lire des données dans une topologie à partir d’une source externe. Les principaux composants d’un BEC VERSEUR sont les suivantes :

    - **NextTuple**: appelé par tempête lorsque le bec n’est autorisé à émettre de nouveau tuples.

    - **Accusé de réception** (topologie transactionnelle uniquement) : gère les accusés de réception initiées par d’autres composants de la topologie de tuples envoyés à partir de ce bec. Accuser réception d’un tuple, cela permet à bec de savoir qu’il a été traité avec succès par les composants en aval.

    - **Échec** (topologie transactionnelle uniquement) : gère les tuples qui sont fail-traitement des autres composants de la topologie. Cela permet d’émettre de nouveau tuple afin qu’elle peut être traitée à nouveau.

2. Remplacez le contenu de la classe **BEC VERSEUR** par celui-ci. Cette opération crée un BEC VERSEUR qui aléatoirement émet une phrase dans la topologie.

        private Context ctx;
        private Random r = new Random();
        string[] sentences = new string[] {
            "the cow jumped over the moon",
            "an apple a day keeps the doctor away",
            "four score and seven years ago",
            "snow white and the seven dwarfs",
            "i am at two with nature"
        };

        public Spout(Context ctx)
        {
            // Set the instance context
            this.ctx = ctx;

            Context.Logger.Info("Generator constructor called");

            // Declare Output schema
            Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
            // The schema for the default output stream is
            // a tuple that contains a string field
            outputSchema.Add("default", new List<Type>() { typeof(string) });
            this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
        }

        // Get an instance of the spout
        public static Spout Get(Context ctx, Dictionary<string, Object> parms)
        {
            return new Spout(ctx);
        }

        public void NextTuple(Dictionary<string, Object> parms)
        {
            Context.Logger.Info("NextTuple enter");
            // The sentence to be emitted
            string sentence;

            // Get a random sentence
            sentence = sentences[r.Next(0, sentences.Length - 1)];
            Context.Logger.Info("Emit: {0}", sentence);
            // Emit it
            this.ctx.Emit(new Values(sentence));

            Context.Logger.Info("NextTuple exit");
        }

        public void Ack(long seqId, Dictionary<string, Object> parms)
        {
            // Only used for transactional topologies
        }

        public void Fail(long seqId, Dictionary<string, Object> parms)
        {
            // Only used for transactional topologies
        }
    
    Prenez quelques instants pour lire les commentaires de comprendre ce que fait ce code.

### <a name="implement-the-bolts"></a>Implémenter les boulons

1. Supprimez le fichier **Bolt.cs** existant dans le projet.

2. Dans l' **Explorateur de solutions**, cliquez sur le projet et sélectionnez **Ajouter** > **un nouvel élément**. Dans la liste, sélectionnez le **Boulon de tempête**et entrez **Splitter.cs** comme nom. Répétez l’opération pour créer un boulon nommé deuxième **Counter.cs**.

    - **Splitter.cs**: implémente un boulon qui fractionne les phrases en mots individuels et émet un flux de données de mots.

    - **Counter.cs**: implémente un boulon qui compte chaque mot et émet un flux de données de mots et le nombre pour chaque mot.

    > [AZURE.NOTE] Ces boulons, simplement lire et écrire dans des flux, mais vous pouvez également utiliser un boulon pour communiquer avec des sources telles qu’une base de données ou un service.

3. Ouvrez **Splitter.cs**. Notez qu’il a une seule méthode par défaut : **Execute**. Cela est appelé lorsque le boulon reçoit un tuple pour traitement. Ici, vous pouvez lire et traiter des tuples entrants et d’émettre des tuples sortant.

4. Remplacez le contenu de la classe de **fractionnement** par le code suivant :

        private Context ctx;

        // Constructor
        public Splitter(Context ctx)
        {
            Context.Logger.Info("Splitter constructor called");
            this.ctx = ctx;

            // Declare Input and Output schemas
            Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
            // Input contains a tuple with a string field (the sentence)
            inputSchema.Add("default", new List<Type>() { typeof(string) });
            Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
            // Outbound contains a tuple with a string field (the word)
            outputSchema.Add("default", new List<Type>() { typeof(string) });
            this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
        }

        // Get a new instance of the bolt
        public static Splitter Get(Context ctx, Dictionary<string, Object> parms)
        {
            return new Splitter(ctx);
        }

        // Called when a new tuple is available
        public void Execute(SCPTuple tuple)
        {
            Context.Logger.Info("Execute enter");

            // Get the sentence from the tuple
            string sentence = tuple.GetString(0);
            // Split at space characters
            foreach (string word in sentence.Split(' '))
            {
                Context.Logger.Info("Emit: {0}", word);
                //Emit each word
                this.ctx.Emit(new Values(word));
            }

            Context.Logger.Info("Execute exit");
        }

    Prenez quelques instants pour lire les commentaires de comprendre ce que fait ce code.

5. Ouvrez **Counter.cs** et remplacez le contenu de classe par le suivant :

        private Context ctx;

        // Dictionary for holding words and counts
        private Dictionary<string, int> counts = new Dictionary<string, int>();

        // Constructor
        public Counter(Context ctx)
        {
            Context.Logger.Info("Counter constructor called");
            // Set instance context
            this.ctx = ctx;

            // Declare Input and Output schemas
            Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
            // A tuple containing a string field - the word
            inputSchema.Add("default", new List<Type>() { typeof(string) });

            Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
            // A tuple containing a string and integer field - the word and the word count
            outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
            this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
        }

        // Get a new instance
        public static Counter Get(Context ctx, Dictionary<string, Object> parms)
        {
            return new Counter(ctx);
        }

        // Called when a new tuple is available
        public void Execute(SCPTuple tuple)
        {
            Context.Logger.Info("Execute enter");

            // Get the word from the tuple
            string word = tuple.GetString(0);
            // Do we already have an entry for the word in the dictionary?
            // If no, create one with a count of 0
            int count = counts.ContainsKey(word) ? counts[word] : 0;
            // Increment the count
            count++;
            // Update the count in the dictionary
            counts[word] = count;

            Context.Logger.Info("Emit: {0}, count: {1}", word, count);
            // Emit the word and count information
            this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word, count));
            Context.Logger.Info("Execute exit");
        }

    Prenez quelques instants pour lire les commentaires de comprendre ce que fait ce code.

### <a name="define-the-topology"></a>Définir la topologie

Becs verseurs et boulons sont organisées dans un graphique, qui définit comment les données circulent entre les composants. Dans cette topologie, le graphique est la suivante :

![image de la façon dont les composants sont organisés.](./media/hdinsight-storm-develop-csharp-visual-studio-topology/wordcount-topology.png)

Phrases sont émis à partir de bec, qui sont distribués aux instances du boulon séparateur. Le boulon de fractionnement divise les phrases en mots, qui sont distribués sur le boulon de compteur.

Parce que le nombre de mots est conservée localement dans l’instance du compteur, nous souhaitons vous assurer que le flux de mots spécifiques à la même instance de boulon de compteur, afin de nous n'avoir qu’une seule instance, suivi d’un mot spécifique. Mais pour le boulon de fractionnement, très peu le boulon reçoit la phrase, nous voulons simplement phrases du solde de la charge sur ces instances.

Ouvrez **Program.cs**. La méthode importante est **GetTopologyBuilder**, qui est utilisée pour définir la topologie qui est soumise à la tempête. Remplacez le contenu de **GetTopologyBuilder** par le code suivant pour implémenter la topologie décrite précédemment :

        // Create a new topology named 'WordCount'
        TopologyBuilder topologyBuilder = new TopologyBuilder("WordCount" + DateTime.Now.ToString("yyyyMMddHHmmss"));

        // Add the spout to the topology.
        // Name the component 'sentences'
        // Name the field that is emitted as 'sentence'
        topologyBuilder.SetSpout(
            "sentences",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"sentence"}}
            },
            1);
        // Add the splitter bolt to the topology.
        // Name the component 'splitter'
        // Name the field that is emitted 'word'
        // Use suffleGrouping to distribute incoming tuples
        //   from the 'sentences' spout across instances
        //   of the splitter
        topologyBuilder.SetBolt(
            "splitter",
            Splitter.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"word"}}
            },
            1).shuffleGrouping("sentences");

        // Add the counter bolt to the topology.
        // Name the component 'counter'
        // Name the fields that are emitted 'word' and 'count'
        // Use fieldsGrouping to ensure that tuples are routed
        //   to counter instances based on the contents of field
        //   position 0 (the word). This could also have been
        //   List<string>(){"word"}.
        //   This ensures that the word 'jumped', for example, will always
        //   go to the same instance
        topologyBuilder.SetBolt(
            "counter",
            Counter.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"word", "count"}}
            },
            1).fieldsGrouping("splitter", new List<int>() { 0 });

        // Add topology config
        topologyBuilder.SetTopologyConfig(new Dictionary<string, string>()
        {
            {"topology.kryo.register","[\"[B\"]"}
        });

        return topologyBuilder;

Prenez quelques instants pour lire les commentaires de comprendre ce que fait ce code.

## <a name="submit-the-topology"></a>Soumettre la topologie

1. Dans l' **Explorateur de solutions**, droit sur le projet et sélectionnez **Envoyer à tempête sur HDInsight**.

    > [AZURE.NOTE] Si vous y êtes invité, entrez les informations d’identification de connexion pour votre abonnement Azure. Si vous avez plus d’un abonnement, connectez-vous à celui qui contient votre tempête sur cluster de HDInsight.

2. Sélectionnez votre tempête sur cluster de HDInsight dans la liste déroulante de **Storm Cluster** et sélectionnez **Envoyer**. Vous pouvez contrôler si l’envoi a réussi à l’aide de la fenêtre **sortie** .

3. Lorsque la topologie a été envoyée avec succès, les **Topologies de Storm** pour le cluster doit apparaître. Sélectionnez la topologie **WordCount** à partir de la liste pour afficher des informations sur la topologie en cours d’exécution.

    > [AZURE.NOTE] Vous pouvez également afficher des **Topologies de tempête** à partir de **l’Explorateur de serveurs**: développez **Azure** > **HDInsight**, avec le bouton droit d’une tempête sur cluster de HDInsight et sélectionnez **Affichage bourrasque de Topologies**.

    Utilisez les liens pour les boulons becs verseurs pour afficher des informations sur ces composants. Une nouvelle fenêtre sera ouvert pour chaque élément sélectionné.

4. À partir de la vue **Résumé de la topologie** , cliquez sur **Arrêter** pour arrêter la topologie.

    > [AZURE.NOTE] Topologies de tempête continuent à s’exécuter jusqu'à ce qu’ils sont désactivés, ou que le cluster est supprimé.

## <a name="transactional-topology"></a>Topologie transactionnelle

La topologie précédente est non transactionnelle. Les composants de la topologie n’implémentent pas toutes les fonctionnalités de la relecture des messages en cas de traitement par un composant dans la topologie. Pour un exemple de topologie transactionnelle, créez un nouveau projet et sélectionnez **Exemple de tempête** comme type de projet.

Topologies transactionnelles implémentent les éléments suivants pour prendre en charge la relecture des données :

- **La mise en cache des métadonnées**: bec doit stocker les métadonnées sur les données émises afin que les données peuvent être récupérées et émises à nouveau si une défaillance se produit. Dans la mesure où les données émises par l’échantillon sont de petite taille, les données brutes pour chaque tuple sont stockées dans un dictionnaire pour la relecture.

- **Accusé de réception**: chaque boulon dans la topologie peut appeler `this.ctx.Ack(tuple)` à l’accusé de réception qu’il a correctement traité un tuple. Lorsque tous les boulons n’ont reçu le tuple, les `Ack` est appelé méthode du bec. Cela permet de bec supprimer les données mises en cache, pour relecture, car les données a été complètement traitées.

- **Échec**: chaque boulon peut appeler `this.ctx.Fail(tuple)` pour indiquer que le traitement a échoué pour un tuple. La défaillance se propage vers le `Fail` méthode du bec, où le tuple peut encore être relu à l’aide des métadonnées mises en cache.

- **ID de séquence**: lors de l’émission d’un tuple, un ID de séquence peut être spécifié. Ce doit être une valeur qui identifie le tuple pour le traitement de relecture (Ack et échec). Par exemple, le bec dans **l’Exemple de la tempête** de projet utilise les éléments suivants lors de l’émission de données :

        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);

    Il émet un nouveau tuple contienne une phrase dans le flux par défaut, avec la valeur d’ID de séquence contenue dans **lastSeqId**. Pour cet exemple, **lastSeqId** est simplement incrémenté pour chaque tuple émis.

Comme illustré dans **l’Exemple de la tempête** de projet, si un composant est transactionnel peut être définie au moment de l’exécution, en fonction de la configuration.

## <a name="hybrid-topology"></a>Topologie de hybride

HDInsight outils de Visual Studio peuvent également être utilisés pour créer des topologies hybrides, où certains composants sont C# et d’autres Java.

Pour un exemple de topologie hybride, créez un nouveau projet et sélectionnez **Exemple hybride de tempête**. Cette opération crée un exemple commenté qui contient plusieurs topologies qui démontrent ce qui suit :

- **Bec de Java** et **C# boulon**: défini dans **HybridTopology_javaSpout_csharpBolt**

    - Une version transactionnelle est définie dans **HybridTopologyTx_javaSpout_csharpBolt**

- **Bec de C#** et de **boulon de Java**: défini dans **HybridTopology_csharpSpout_javaBolt**

    - Une version transactionnelle est définie dans **HybridTopologyTx_csharpSpout_javaBolt**

        > [AZURE.NOTE] Cette version illustre également comment utiliser le code de Clojure à partir d’un fichier texte sous la forme d’un composant Java.

Pour basculer entre la topologie qui est utilisée lorsque le projet est soumis, il suffit de déplacer le `[Active(true)]` instruction à la topologie que vous souhaitez utiliser avant de les envoyer au cluster.

> [AZURE.NOTE] Tous les fichiers de Java sont nécessaires sont fournies dans le cadre de ce projet dans le dossier **JavaDependency** .

Prenez en compte ce qui suit lors de la création et la soumission d’une topologie hybride :

- **JavaComponentConstructor** doit être utilisé pour créer une nouvelle instance de la classe Java d’un BEC VERSEUR ou le boulon.

- **microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer** doit être utilisé pour sérialiser des données dans ou hors les composants Java à partir d’objets de Java en JSON.

- Lors de l’envoi de la topologie pour le serveur, vous devez utiliser l’option de **configurations supplémentaires** pour spécifier les **chemins d’accès des fichiers de Java**. Le chemin d’accès spécifié doit être le répertoire qui contient les fichiers JAR contenant vos classes Java.

### <a name="azure-event-hubs"></a>Événement Azure concentrateurs

SCP.Net version 0.9.4.203 introduit une nouvelle classe et la méthode spécifiquement pour l’utilisation de l’événement concentrateur BEC VERSEUR (bec de Java qui lit à partir du concentrateur d’événements.) Lors de la création d’une topologie qui utilise ce bec, appliquez les méthodes suivantes :

- Classe de **EventHubSpoutConfig** : crée un objet qui contient la configuration pour le composant de BEC VERSEUR

- Méthode de **TopologyBuilder.SetEventHubSpout** : ajoute le composant événement bec de Hub à la topologie

> [AZURE.NOTE] Alors qu’ils rendent plus facile de travailler avec le Hub événement BEC VERSEUR à d’autres composants Java, vous devez toujours utiliser le CustomizedInteropJSONSerializer pour sérialiser les données produites par le bec.

## <a id="configurationmanager"></a>À l’aide de ConfigurationManager

Ne pas utiliser ConfigurationManager pour récupérer les valeurs de configuration à partir de boulon et BEC VERSEUR de composants ; Cela entraîne une exception de pointeur null. Au lieu de cela, la configuration de votre projet est passée dans la topologie de la tempête sous la forme d’une paire clé/valeur dans le contexte de la topologie. Chaque composant qui repose sur les valeurs de configuration doit récupérer à partir du contexte lors de l’initialisation.

Le code suivant illustre comment récupérer ces valeurs :

    public class MyComponent : ISCPBolt
    {
        // To hold configuration information loaded from context
        Configuration configuration;
        ...
        public MyComponent(Context ctx, Dictionary<string, Object> parms)
        {
            // Save a copy of the context for this component instance
            this.ctx = ctx;
            // If it exists, load the configuration for the component
            if(parms.ContainsKey(Constants.USER_CONFIG))
            {
                this.configuration = parms[Constants.USER_CONFIG] as System.Configuration.Configuration;
            }
            // Retrieve the value of "Foo" from configuration
            var foo = this.configuration.AppSettings.Settings["Foo"].Value;
        }
        ...
    }

Si vous utilisez un `Get` méthode pour retourner une instance de votre composant, vous devez vous assurer qu’il passe à la fois la `Context` et `Dictionary<string, Object>` paramètres du constructeur. L’exemple suivant est un basic `Get` méthode qui passe correctement les valeurs suivantes :

    public static MyComponent Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new MyComponent(ctx, parms);
    }

## <a name="how-to-update-scpnet"></a>La mise à jour SCP.NET

Les versions récentes de SCP.NET prend en charge la mise à niveau de package via NuGet. Lorsqu’une nouvelle mise à jour est disponible, vous recevrez une notification de mise à niveau. Pour rechercher manuellement une mise à niveau, procédez comme suit :

1. Dans l' **Explorateur de solutions**, cliquez sur le projet et sélectionnez **Manage NuGet Packages**.

2. Dans le Gestionnaire de package, sélectionnez **mises à jour**. Si une mise à jour est disponible, il doit être répertorié. Cliquez sur le bouton de **mise à jour** pour le package d’installation.

> [AZURE.IMPORTANT] Si votre projet a été créé avec l’une des versions antérieures de SCP.NET qui n’utilise pas de NuGet pour les mises à jour de package, vous devez effectuer les étapes suivantes pour mettre à jour vers la nouvelle version :
>
> 1. Dans l' **Explorateur de solutions**, cliquez sur le projet et sélectionnez **Manage NuGet Packages**.
> 2. À l’aide du champ de **recherche** , recherchez et puis ajouter, **Microsoft.SCP.Net.SDK** au projet.

## <a name="troubleshooting"></a>Résolution des problèmes

### <a name="null-pointer-exceptions"></a>Exceptions de pointeur null

Lorsque vous utilisez une topologie C# avec un cluster basé sur Linux de HDInsight, de boulon et BEC VERSEUR de composants qui permet de lire les paramètres de configuration au moment de l’exécution peuvent renvoyer des exceptions de pointeur null ConfigurationManager. Cela se produit car la configuration pour le domaine chargé n’est pas à partir de l’assembly qui contient votre projet.

La configuration de votre projet est passée dans la topologie de la tempête sous la forme d’une paire clé/valeur dans le contexte de la topologie et peut être récupérée à partir de l’objet de dictionnaire qui est transmis à vos composants lorsqu’elles sont initialisées.

L’exemple suivant montre le chargement les valeurs de configuration à partir du contexte de topologie, reportez-vous à la section [ConfigurationManager](#configurationmanager) de ce document.

### <a name="systemtypeloadexception"></a>System.TypeLoadException

Lorsque vous utilisez une topologie C# avec un cluster basé sur Linux de HDInsight, vous pouvez rencontrer l’erreur suivante :

    System.TypeLoadException: Failure has occurred while loading a type.

En général, cette survient lors de l’utilisation d’un fichier binaire qui n’est pas compatible avec la version de .NET qui la prend en charge mono.

Pour les clusters de HDInsight de basé sur Linux, il se peut que vous devez vous assurer que votre projet utilise des fichiers binaires compilés pour .NET 4.5.


### <a name="test-a-topology-locally"></a>Tester une topologie localement

Bien qu’il soit facile à déployer une topologie à un cluster, dans certains cas, vous devrez peut-être tester une topologie localement. Procédez comme suit pour exécuter et tester l’exemple de topologie dans ce didacticiel localement dans votre environnement de développement.

> [AZURE.WARNING] Des tests locaux ne fonctionnement que pour les topologies seules base, C#. Vous ne devez pas utiliser un test local pour les topologies hybrides ou des topologies qui utilisent plusieurs flux de données, que vous recevrez des erreurs.

1. Dans l' **Explorateur de solutions**, cliquez sur le projet et sélectionnez **Propriétés**. Dans les propriétés du projet, modifiez le **type de sortie** pour une **Application Console**.

    ![type de sortie](./media/hdinsight-storm-develop-csharp-visual-studio-topology/outputtype.png)

    > [AZURE.NOTE] N’oubliez pas de modifier le **type de sortie** à **La bibliothèque de classe** avant de déployer la topologie d’un cluster.

2. Dans l' **Explorateur de solutions**, cliquez sur le projet, puis sélectionnez **Ajouter** > **Un nouvel élément**. Sélectionnez la **classe** et entrez **LocalTest.cs** comme nom de classe. Enfin, cliquez sur **Ajouter**.

3. Ouvrez **LocalTest.cs** et ajoutez l’instruction **using** suivante en haut :

        using Microsoft.SCP;

4. Comme le contenu de la classe **LocalTest** , utilisez ce qui suit :

        // Drives the topology components
        public void RunTestCase()
        {
            // An empty dictionary for use when creating components
            Dictionary<string, Object> emptyDictionary = new Dictionary<string, object>();

            #region Test the spout
            {
                Console.WriteLine("Starting spout");
                // LocalContext is a local-mode context that can be used to initialize
                // components in the development environment.
                LocalContext spoutCtx = LocalContext.Get();
                // Get a new instance of the spout, using the local context
                Spout sentences = Spout.Get(spoutCtx, emptyDictionary);

                // Emit 10 tuples
                for (int i = 0; i < 10; i++)
                {
                    sentences.NextTuple(emptyDictionary);
                }
                // Use LocalContext to persist the data stream to file
                spoutCtx.WriteMsgQueueToFile("sentences.txt");
                Console.WriteLine("Spout finished");
            }
            #endregion

            #region Test the splitter bolt
            {
                Console.WriteLine("Starting splitter bolt");
                // LocalContext is a local-mode context that can be used to initialize
                // components in the development environment.
                LocalContext splitterCtx = LocalContext.Get();
                // Get a new instance of the bolt
                Splitter splitter = Splitter.Get(splitterCtx, emptyDictionary);


                // Set the data stream to the data created by the spout
                splitterCtx.ReadFromFileToMsgQueue("sentences.txt");
                // Get a batch of tuples from the stream
                List<SCPTuple> batch = splitterCtx.RecvFromMsgQueue();
                // Process each tuple in the batch
                foreach (SCPTuple tuple in batch)
                {
                    splitter.Execute(tuple);
                }
                // Use LocalContext to persist the data stream to file
                splitterCtx.WriteMsgQueueToFile("splitter.txt");
                Console.WriteLine("Splitter bolt finished");
            }
            #endregion

            #region Test the counter bolt
            {
                Console.WriteLine("Starting counter bolt");
                // LocalContext is a local-mode context that can be used to initialize
                // components in the development environment.
                LocalContext counterCtx = LocalContext.Get();
                // Get a new instance of the bolt
                Counter counter = Counter.Get(counterCtx, emptyDictionary);

                // Set the data stream to the data created by splitter bolt
                counterCtx.ReadFromFileToMsgQueue("splitter.txt");
                // Get a batch of tuples from the stream
                List<SCPTuple> batch = counterCtx.RecvFromMsgQueue();
                // Process each tuple in the batch
                foreach (SCPTuple tuple in batch)
                {
                    counter.Execute(tuple);
                }
                // Use LocalContext to persist the data stream to file
                counterCtx.WriteMsgQueueToFile("counter.txt");
                Console.WriteLine("Counter bolt finished");
            }
            #endregion
        }

    Prenez quelques instants pour lire les commentaires de code. Ce code utilise **LocalContext** pour exécuter les composants dans l’environnement de développement, et il persiste le flux de données entre les composants dans des fichiers texte sur le disque local.

5. Ouvrez **Program.cs** et ajoutez le code suivant à la méthode **Main** :

        Console.WriteLine("Starting tests");
        System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-LocalTest");
        // Initialize the runtime
        SCPRuntime.Initialize();

        //If we are not running under the local context, throw an error
        if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
        {
            throw new Exception(string.Format("unexpected pluginType: {0}", Context.pluginType));
        }
        // Create test instance
        LocalTest tests = new LocalTest();
        // Run tests
        tests.RunTestCase();
        Console.WriteLine("Tests finished");
        Console.ReadKey();

6. Enregistrer les modifications, puis cliquez sur **F5** ou sélectionnez **débogage** > **Démarrer le débogage** pour démarrer le projet. Une fenêtre de console doit s’afficher et l’état du journal que l’avancement des tests. Lorsque **les Tests terminé** s’affiche, appuyez sur une touche pour fermer la fenêtre.

7. Utilisez **L’Explorateur Windows** pour localiser le répertoire qui contient votre projet, par exemple, **C:\Users\<votre_nom_utilisateur > \Documents\Visual Studio 2013\Projects\WordCount\WordCount**. Dans ce répertoire, ouvrez **l’emplacement**, puis cliquez sur **Debug**. Vous devez voir les fichiers de texte qui ont été générés lors de l’exécution de tests : sentences.txt, counter.txt et splitter.txt. Ouvrez chaque fichier texte et inspecter les données.

    > [AZURE.NOTE] Données de type chaîne sont rendu persistant comme un tableau de valeurs décimales dans ces fichiers. Par exemple, \[[97,103,111]] dans le **splitter.txt** , le fichier est le mot « et ».

Bien que le nombre de tests d’un mot de base application localement est relativement triviale, que la valeur réelle est fourni lorsque vous avez une topologie complexe qui communique avec des sources de données externes ou effectue l’analyse de données complexes. Lorsque vous travaillez sur un projet de ce type, vous devrez définir des points d’arrêt et de parcourir le code de vos composants à isoler les problèmes.

> [AZURE.NOTE] Veillez à définir le **type de projet** à **La bibliothèque de classe** avant de déployer dans une tempête sur cluster de HDInsight.

### <a name="log-information"></a>Informations du journal

Vous pouvez facilement enregistrer des informations à partir de vos composants de la topologie à l’aide de `Context.Logger`. Par exemple, le texte suivant crée une entrée d’information du journal :

    Context.Logger.Info("Component started");

Informations consignées sont consultables à partir du **Journal du Service d’Hadoop**, qui se trouve dans **l’Explorateur de serveurs**. Développez l’entrée pour votre tempête sur cluster de HDInsight, puis **Hadoop Service journal**. Enfin, sélectionnez le fichier journal à afficher.

> [AZURE.NOTE] Les journaux sont stockés dans le compte de stockage Azure qui est utilisé par le cluster. S’il s’agit d’un abonnement différent que celui que vous êtes connecté à avec Visual Studio, vous avez besoin pour vous connecter à l’abonnement qui contient le compte de stockage pour afficher ces informations.

### <a name="view-error-information"></a>Affichage des informations d’erreur

Pour afficher les erreurs qui sont sont produites dans une topologie en cours d’exécution, procédez comme suit :

1. Dans l' **Explorateur de serveurs**, avec le bouton droit de la tempête sur cluster de HDInsight et sélectionnez **affichage tempête topologies**.

2. Pour le **bec** et des **boulons**, la colonne **Dernière erreur** aura d’informations sur la dernière erreur qui s’est produite.

3. Sélectionnez le **Code de BEC VERSEUR** ou **Boulon** pour le composant qui a une erreur répertoriée. Sur la page de détails qui s’affiche, les informations d’erreur supplémentaires apparaît dans la section **d’erreurs** en bas de la page.

4. Pour obtenir plus d’informations, sélectionnez un **Port** de la section **les exécuteurs** de la page pour voir le journal du travailleur Storm pour les dernières minutes.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment développer et déployer des topologies de tempête à partir des outils HDInsight pour Visual Studio, découvrez comment [traiter les événements de concentrateur d’événements Azure avec tempête sur HDInsight](hdinsight-storm-develop-csharp-event-hub-topology.md).

Pour obtenir un exemple d’une topologie de C# qui scinde les données de flux en plusieurs flux de données, consultez [exemple de C# tempête](https://github.com/Blackmist/csharp-storm-example).

Pour découvrir plus d’informations sur la création de topologies de C#, visitez [SCP.NET GettingStarted.md](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md).

Pour les autres façons de travailler avec HDInsight et Storm plus sur des échantillons de HDinsight, consultez les rubriques suivantes :

**Microsoft SCP.NET**

* [Guide de programmation SCP](hdinsight-storm-scp-programming-guide.md)

**Tempête de Apache sur HDInsight**

- [Déployer et surveiller des topologies avec tempête Apache sur HDInsight](hdinsight-storm-deploy-monitor-topology.md)

- [Exemples de topologies pour tempête sur HDInsight](hdinsight-storm-example-topology.md)

**Hadoop Apache sur HDInsight**

- [Utilisez la ruche avec Hadoop sur HDInsight](hdinsight-use-hive.md)

- [Utilisez des porcs avec Hadoop sur HDInsight](hdinsight-use-pig.md)

- [Utilisez MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)

**HBase Apache sur HDInsight**

- [Mise en route avec HBase sur HDInsight](hdinsight-hbase-tutorial-get-started.md)
