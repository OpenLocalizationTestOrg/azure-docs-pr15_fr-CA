<properties
    pageTitle="Sérialiser des données à l’aide de Microsoft Avro Library | Microsoft Azure"
    description="Découvrez comment les HDInsight d’Azure utilise Avro pour sérialiser des données volumineuses."
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian" 
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="jgao"/>


# <a name="serialize-data-in-hadoop-with-the-microsoft-avro-library"></a>Sérialiser des données Hadoop avec Microsoft Avro Library

Cette rubrique montre comment utiliser la <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">Bibliothèque Avro de Microsoft</a> pour sérialiser des objets et autres structures de données dans le flux de données afin de les rendre persistants dans la mémoire, une base de données ou un fichier et également comment désérialiser les pour récupérer les objets d’origine.

[AZURE.INCLUDE [windows-only](../../includes/hdinsight-windows-only.md)]

##<a name="apache-avro"></a>Apache Avro
La <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">Bibliothèque Avro de Microsoft</a> met en œuvre le système de sérialisation de données Apache Avro pour l’environnement Microsoft.NET. Apache Avro fournit un format d’échange des données binaires compact pour la sérialisation. Il utilise <a href="http://www.json.org" target="_blank">JSON</a> pour définir un schéma indépendant du langage qui couvre l’interopérabilité des langages. Données sérialisées dans un seul langage peuvent être lu dans un autre. En C, C++, C#, Java, PHP, Python et Ruby sont pris en charge. Vous trouverez des informations détaillées sur le format de la <a href="http://avro.apache.org/docs/current/spec.html" target="_blank">Spécification de Avro Apache</a>. Notez que la version actuelle de Microsoft Avro Library ne prend pas en charge la partie appels de procédure distante de cette spécification.

La représentation sérialisée d’un objet dans le système de Avro se compose de deux parties : schéma et la valeur réelle. Le schéma Avro décrit le modèle de données indépendant du langage des données sérialisées avec JSON. Il est à présent côte à côte avec une représentation binaire des données. Ayant le schéma distinct à partir de la représentation binaire permet à chaque objet à écrire avec aucun frais généraux par valeur, fabrication rapide de sérialisation et de la représentation sous forme de petites.

##<a name="the-hadoop-scenario"></a>Le scénario d’Hadoop
Le format de sérialisation Apache Avro est largement utilisé dans les HDInsight d’Azure et d’autres environnements de Apache Hadoop. Avro offre un moyen pratique pour représenter des structures de données complexes dans un travail d’Hadoop MapReduce. Le format des fichiers de Avro (fichier de conteneur objet Avro) a été conçu en charge le modèle de programmation distribué MapReduce. La fonctionnalité clé qui permet la distribution est que les fichiers sont « séparables » dans le sens où une peut rechercher n’importe quel point dans un fichier et le démarrage de la lecture à partir d’un bloc particulier.

##<a name="serialization-in-avro-library"></a>Sérialisation dans la bibliothèque de Avro
La bibliothèque .NET pour Avro prend en charge deux modes de sérialisation d’objets :

- **réflexion** - schéma le JSON pour les types d’est généré automatiquement à partir des données des attributs de contrat des types .NET à sérialiser.
- **enregistrement générique** - JSON d’un schéma est explicitement spécifié dans un enregistrement représenté par la classe [**AvroRecord**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) lorsque aucun des types .NET ne sont présents pour décrire le schéma pour les données à sérialiser.

Lorsque le schéma de données est connu pour le writer et le lecteur du flux, les données peuvent être envoyées sans son schéma. Dans le cas lorsqu’un fichier de conteneur d’objet Avro est utilisé, le schéma est stocké dans le fichier. Autres paramètres, tels que le codec utilisé pour la compression de données, peuvent être spécifiés. Ces scénarios sont décrits plus en détail et illustrés dans les exemples de code ci-dessous.


## <a name="install-avro-library"></a>Installez la bibliothèque de Avro

Les éléments suivants sont requis avant d’installer la bibliothèque :

- <a href="http://www.microsoft.com/download/details.aspx?id=17851" target="_blank">Microsoft.NET Framework 4</a>
- <a href="http://james.newtonking.com/json" target="_blank">Newtonsoft Json.NET</a> (6.0.4 ou ultérieur)

Notez que la dépendance de Newtonsoft.Json.dll est automatiquement téléchargée avec l’installation de Microsoft Avro Library. Cette procédure est fournie dans la section suivante.


Microsoft Avro Library est distribué sous forme de package NuGet qui peut être installé à partir de Visual Studio via la procédure suivante :

1. Sélectionnez l’onglet **projet** -> **Manage NuGet Packages...**
2. Recherchez « Microsoft.Hadoop.Avro » dans la zone de **Recherche en ligne** .
3. Cliquez sur le bouton **installer** **Microsoft Azure HDInsight Avro de la bibliothèque**.

Notez que le Newtonsoft.Json.dll (> = 6.0.4) dépendance est également téléchargé automatiquement avec Microsoft Avro Library.

Voulez-vous visiter la <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">page d’accueil Microsoft Avro bibliothèque</a> pour lire les notes de publication.


Le code source de Microsoft Avro Library est disponible sur la <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">page d’accueil Microsoft Avro bibliothèque</a>.

##<a name="compile-schemas-using-avro-library"></a>Compiler des schémas à l’aide de la bibliothèque de Avro

Microsoft Avro Library contient un utilitaire de génération de code qui permet de créer les types C# automatiquement basés sur le schéma JSON défini précédemment. L’utilitaire de génération de code n’est pas distribué sous la forme d’un fichier exécutable binaire, mais peut être facilement créé via la procédure suivante :

1. Téléchargez le fichier .zip avec la dernière version du code source de HDInsight SDK <a href="http://hadoopsdk.codeplex.com/SourceControl/latest#" target="_blank">Microsoft .NET SDK pour Hadoop</a>. (Cliquez sur l’icône **Télécharger** , pas l’onglet **téléchargements** .)

2. Extrayez le SDK HDInsight dans un répertoire sur l’ordinateur avec le.NET Framework 4 installé et connecté à Internet pour télécharger les packages NuGet de dépendance nécessaire. Ci-dessous, nous supposerons que le code source est extraite de C:\SDK.

3. Accédez au dossier C:\SDK\src\Microsoft.Hadoop.Avro.Tools et exécutez build.bat. (Le fichier appelle MSBuild à partir de la distribution 32 bits du.NET Framework. Si vous souhaitez utiliser la version 64 bits, modifier build.bat, suivant les commentaires dans le fichier.) Assurez-vous que la génération a réussi. (Sur certains systèmes, MSBuild peut-être produire des avertissements. Ces avertissements n’affectent pas l’utilitaire tant qu’il n’y a aucune erreur de génération.)

4. L’utilitaire compilée se trouve dans C:\SDK\Bin\Unsigned\Release\Microsoft.Hadoop.Avro.Tools.


Pour vous familiariser avec la syntaxe de ligne de commande, exécutez la commande suivante à partir du dossier où se trouve l’utilitaire de génération de code :`Microsoft.Hadoop.Avro.Tools help /c:codegen`

Pour tester l’utilitaire, vous pouvez générer des classes C# à partir de l’exemple de fichier de schéma JSON fourni avec le code source. Exécutez la commande suivante :

    Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:

Il est censé pour produire C# deux fichiers dans le répertoire en cours : SensorData.cs et Location.cs.

Pour comprendre la logique à l’aide de l’utilitaire de génération de code lors de la conversion du schéma JSON en types C#, consultez le fichier que generationverification.Feature situé dans C:\SDK\src\Microsoft.Hadoop.Avro.Tools\Doc.

Veuillez noter que les espaces de noms sont extraits à partir du schéma JSON, à l’aide de la logique décrite dans le fichier mentionné dans le paragraphe précédent. Extrait le schéma des espaces de noms sont prioritaires sur tout ce qui est fourni avec le paramètre /n dans la ligne de commande utilitaire. Si vous souhaitez substituer les espaces de noms contenus dans le schéma, utilisez le paramètre /nf. Par exemple, pour convertir tous les espaces de noms à partir de la SampleJSONSchema.avsc my.own.nspace, exécutez la commande suivante :

    Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:. /nf:my.own.nspace

## <a name="samples"></a>Exemples
Six exemples fournis dans cette rubrique illustrent différents scénarios pris en charge par Microsoft Avro Library. Microsoft Avro Library est conçu pour fonctionner avec n’importe quel flux de données. Dans ces exemples, les données sont manipulées via les flux de mémoire plutôt que de flux de fichiers ou de bases de données pour des raisons de simplicité et de cohérence. L’approche adoptée dans un environnement de production dépendra des exigences du scénario exacte, source de données et de volume, des contraintes de performance et d’autres facteurs.

Les deux premiers exemples montrent comment sérialiser et désérialiser les données dans la mémoire tampon de flux de données à l’aide de la réflexion et enregistrements génériques. Le schéma dans ces deux cas est supposé être partagées entre les lecteurs et writers out-of-band.

Les troisième et quatrième illustrent comment sérialiser et désérialiser les données en utilisant les fichiers de conteneur d’objet Avro. Lorsque les données sont stockées dans un fichier de conteneur de Avro, son schéma est toujours stocké avec lui parce que le schéma doit être partagé pour la désérialisation.

L’échantillon contenant les quatre premiers exemples peut être téléchargé à partir du site <a href="http://code.msdn.microsoft.com/windowsazure/Serialize-data-with-the-86055923" target="_blank">d’exemples de code Azure</a> .

Le cinquième exemple montre comment utiliser un codec de compression personnalisé pour les fichiers de Avro objet conteneur. Un échantillon contenant le code de cet exemple peut être téléchargé à partir du site <a href="http://code.msdn.microsoft.com/windowsazure/Serialize-data-with-the-67159111" target="_blank">d’exemples de code Azure</a> .

Le sixième exemple indique comment utiliser la sérialisation de la Avro pour charger les données pour le stockage des objets Blob Azure et ensuite l’analyser avec un cluster HDInsight (Hadoop) à l’aide de la ruche. Il peut être téléchargé à partir du site <a href="https://code.msdn.microsoft.com/windowsazure/Using-Avro-to-upload-data-ae81b1e3" target="_blank">d’exemples de code Azure</a> .

Voici des liens vers les six exemples décrits dans la rubrique :

 * <a href="#Scenario1">**Sérialisation avec réflexion**</a> - schéma le JSON pour sérialiser les types générées automatiquement à partir des données des attributs de contrat.
 * <a href="#Scenario2">**Sérialisation avec enregistrement générique**</a> - schéma du JSON est explicitement spécifié dans un enregistrement lorsque aucun type .NET n’est disponible pour la réflexion.
 * <a href="#Scenario3">**Sérialisation à l’aide des fichiers de conteneur d’objet avec la réflexion**</a> - schéma de JSON l’est automatiquement créé et partagé avec les données sérialisées via un fichier de conteneur d’objet Avro.
 * <a href="#Scenario4">**Sérialisation à l’aide des fichiers de conteneur d’objets avec enregistrement générique**</a> - schéma de JSON l’est explicitement spécifiée avant la sérialisation et partagé avec les données via un fichier de conteneur d’objet Avro.
 * <a href="#Scenario5">**Sérialisation à l’aide des fichiers de conteneur d’objets avec un codec de compression personnalisé**</a> - l’exemple montre comment créer un fichier de conteneur d’objet Avro avec une implémentation .NET personnalisée du codec de compression de données Deflate.
 * <a href="#Scenario6">**À l’aide de Avro pour charger les données pour le service Microsoft Azure HDInsight**</a> - l’exemple illustre comment Avro sérialisation interagit avec le service HDInsight. Un abonnement Azure et l’accès à un cluster Azure HDInsight sont requis pour exécuter cet exemple.

###<a name="Scenario1"></a>Exemple 1 : Sérialisation avec la réflexion

Le schéma JSON pour les types peut être automatiquement créé par Microsoft Avro Library par réflexion à partir des données des attributs de contrat des objets C# doit être sérialisé. Microsoft Avro Library crée un [**IAvroSeralizer<T> **](http://msdn.microsoft.com/library/dn627341.aspx) pour identifier les champs doit être sérialisé.

Dans cet exemple, les objets (une classe **SensorData** avec une structure **d’emplacement** de membre) sont sérialisés dans un flux de mémoire, et ce flux est ensuite désérialisé. Le résultat est ensuite comparé à l’instance initiale pour confirmer que l’objet **SensorData** récupérée est identique à l’original.

Le schéma dans cet exemple est supposé être partagées entre les lecteurs et writers, afin que le format de conteneur d’objet Avro n’est pas obligatoire. Pour obtenir un exemple de la façon de sérialiser et de désérialiser des données dans des tampons de mémoire en utilisant la réflexion avec le format de conteneur objet lorsque le schéma doit être partagé avec les données, consultez <a href="#Scenario3">sérialisation à l’aide des fichiers de conteneur d’objet avec la réflexion</a>.

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serialize and deserialize sample data set represented as an object using reflection.
            //No explicit schema definition is required - schema of serialized objects is automatically built.
            public void SerializeDeserializeObjectUsingReflection()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION\n");
                Console.WriteLine("Serializing Sample Data Set...");

                //Create a new AvroSerializer instance and specify a custom serialization strategy AvroDataContractResolver
                //for serializing only properties attributed with DataContract/DateMember
                var avroSerializer = AvroSerializer.Create<SensorData>();

                //Create a memory stream buffer
                using (var buffer = new MemoryStream())
                {
                    //Create a data set by using sample class and struct
                    var expected = new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } };

                    //Serialize the data to the specified stream
                    avroSerializer.Serialize(buffer, expected);


                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Deserialize data from the stream and cast it to the same type used for serialization
                    var actual = avroSerializer.Deserialize(buffer);

                    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                    //Finally, verify that deserialized data matches the original one
                    bool isEqual = this.Equal(expected, actual);

                    Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);

                }
            }

            //
            //Helper methods
            //

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }



            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization to memory using reflection
                Sample.SerializeDeserializeObjectUsingReflection();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION
    //
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


###<a name="sample-2-serialization-with-a-generic-record"></a>Exemple 2 : Sérialisation avec un enregistrement générique

Un schéma JSON peut être explicitement spécifié dans un enregistrement générique lors de la réflexion ne peut pas être utilisée car les données ne peuvent pas être représentées via les classes .NET avec un contrat de données. Cette méthode est généralement plus lente qu’à l’aide de la réflexion. Dans ce cas, le schéma pour les données peuvent également être dynamiques, par exemple, ne pas être connu à la compilation. Données représentées sous forme de fichiers de valeurs séparées par des virgules (CSV) dont le schéma est inconnu jusqu'à ce qu’il est transformé au format Avro au moment de l’exécution sont un exemple de ce type de scénario dynamique.

Cet exemple montre comment créer et utiliser un [**AvroRecord**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) pour spécifier explicitement un schéma JSON, comment la remplir avec les données, puis comment sérialiser et désérialiser. Le résultat est ensuite comparé à l’instance initiale pour confirmer que l’enregistrement récupéré est identique à l’original.

Le schéma dans cet exemple est supposé être partagées entre les lecteurs et writers, afin que le format de conteneur d’objet Avro n’est pas obligatoire. Pour obtenir un exemple illustrant comment sérialiser et désérialiser des données en mémoire tampon à l’aide d’un enregistrement générique avec le format de conteneur objet lorsque le schéma doit être inclus dans les données sérialisées, consultez l’exemple de la <a href="#Scenario4">sérialisation à l’aide des fichiers de conteneur d’objets avec enregistrement générique</a> .


    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    using Microsoft.Hadoop.Avro.Schema;
    using Microsoft.Hadoop.Avro;

    //This class contains all methods demonstrating
    //the usage of Microsoft Avro Library
    public class AvroSample
    {

        //Serialize and deserialize sample data set by using a generic record.
        //A generic record is a special class with the schema explicitly defined in JSON.
        //All serialized data should be mapped to the fields of the generic record,
        //which in turn will be then serialized.
        public void SerializeDeserializeObjectUsingGenericRecords()
        {
            Console.WriteLine("SERIALIZATION USING GENERIC RECORD\n");
            Console.WriteLine("Defining the Schema and creating Sample Data Set...");

            //Define the schema in JSON
            const string Schema = @"{
                                ""type"":""record"",
                                ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
                                ""fields"":
                                    [
                                        {
                                            ""name"":""Location"",
                                            ""type"":
                                                {
                                                    ""type"":""record"",
                                                    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
                                                    ""fields"":
                                                        [
                                                            { ""name"":""Floor"", ""type"":""int"" },
                                                            { ""name"":""Room"", ""type"":""int"" }
                                                        ]
                                                }
                                        },
                                        { ""name"":""Value"", ""type"":""bytes"" }
                                    ]
                            }";

            //Create a generic serializer based on the schema
            var serializer = AvroSerializer.CreateGeneric(Schema);
            var rootSchema = serializer.WriterSchema as RecordSchema;

            //Create a memory stream buffer
            using (var stream = new MemoryStream())
            {
                //Create a generic record to represent the data
                dynamic location = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location.Floor = 1;
                location.Room = 243;

                dynamic expected = new AvroRecord(serializer.WriterSchema);
                expected.Location = location;
                expected.Value = new byte[] { 1, 2, 3, 4, 5 };

                Console.WriteLine("Serializing Sample Data Set...");

                //Serialize the data
                serializer.Serialize(stream, expected);

                stream.Seek(0, SeekOrigin.Begin);

                Console.WriteLine("Deserializing Sample Data Set...");

                //Deserialize the data into a generic record
                dynamic actual = serializer.Deserialize(stream);

                Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                //Finally, verify the results
                bool isEqual = expected.Location.Floor.Equals(actual.Location.Floor);
                isEqual = isEqual && expected.Location.Room.Equals(actual.Location.Room);
                isEqual = isEqual && ((byte[])expected.Value).SequenceEqual((byte[])actual.Value);
                Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);
            }
        }

        static void Main()
        {

            string sectionDivider = "---------------------------------------- ";

            //Create an instance of AvroSample class and invoke methods
            //illustrating different serializing approaches
            AvroSample Sample = new AvroSample();

            //Serialization to memory using generic record
            Sample.SerializeDeserializeObjectUsingGenericRecords();

            Console.WriteLine(sectionDivider);
            Console.WriteLine("Press any key to exit.");
            Console.Read();
        }
    }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING GENERIC RECORD
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


###<a name="sample-3-serialization-using-object-container-files-and-serialization-with-reflection"></a>Exemple 3 : Sérialisation à l’aide des fichiers de conteneur d’objets et de la sérialisation avec la réflexion

Cet exemple est similaire au scénario dans <a href="#Scenario1">premier exemple</a>, où le schéma est spécifié implicitement avec la réflexion. La différence est qu’ici, le schéma n'est pas supposé être connu pour le lecteur qui désérialise. Les objets de **SensorData** doit être sérialisé et leur schéma spécifié de manière implicite sont stockées dans un fichier de conteneur d’objet Avro représenté par la classe [**AvroContainer**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.avrocontainer.aspx) .

Les données sont sérialisées dans cet exemple, avec [**SequentialWriter<SensorData> **](http://msdn.microsoft.com/library/dn627340.aspx) et désérialisé avec [**SequentialReader<SensorData>**](http://msdn.microsoft.com/library/dn627340.aspx). Le résultat est ensuite comparé aux instances pour garantir l’identité initiales.

Les données dans le fichier conteneur objet sont compressées par défaut [**Deflate**] [ deflate-100] codec de compression à partir de.NET Framework 4. Voir <a href="#Scenario5">cinquième exemple</a> de cette rubrique pour savoir comment utiliser une version plus récente et supérieure de la [**Deflate**] [ deflate-110] codec de compression disponible dans.NET Framework 4.5.

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes the sample data set by using reflection and Avro object container files.
            //Serialized data is compressed with the Deflate codec.
            public void SerializeDeserializeUsingObjectContainersReflection()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES\n");

                //Path for Avro object container file
                string path = "AvroSampleReflectionDeflate.avro";

                //Create a data set by using sample class and struct
                var testData = new List<SensorData>
                        {
                            new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
                            new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
                        };

                //Serializing and saving data to file.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Data will be compressed using the Deflate codec.
                    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
                    {
                        using (var writer = new SequentialWriter<SensorData>(w, 24))
                        {
                            // Serialize the data to stream by using the sequential writer
                            testData.ForEach(writer.Write);
                        }
                    }

                    //Save stream to file
                    Console.WriteLine("Saving serialized data to file...");
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing data.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Create a SequentialReader instance for type SensorData, which will deserialize all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    using (var reader = new SequentialReader<SensorData>(
                        AvroContainer.CreateReader<SensorData>(buffer, true)))
                    {
                        var results = reader.Objects;

                        //Finally, verify that deserialized data matches the original one
                        Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
                        int count = 1;
                        var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
                        foreach (var pair in pairs)
                        {
                            bool isEqual = this.Equal(pair.expected, pair.actual);
                            Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual);
                            count++;
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }

            //
            //Helper methods
            //

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading a file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using reflection to Avro object container file
                Sample.SerializeDeserializeUsingObjectContainersReflection();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


###<a name="sample-4-serialization-using-object-container-files-and-serialization-with-generic-record"></a>Exemple 4 : Sérialisation à l’aide des fichiers de conteneur d’objets et de la sérialisation avec enregistrement générique

Cet exemple est similaire au scénario dans <a href="#Scenario2">deuxième exemple</a>, où le schéma est explicitement spécifié avec JSON. La différence est qu’ici, le schéma n'est pas supposé être connu pour le lecteur qui désérialise.

L’ensemble de données test est rassemblé dans une liste d’objets de [**AvroRecord**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) via un schéma JSON défini explicitement et ensuite stockée dans un fichier de conteneur objet représenté par la classe [**AvroContainer**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.avrocontainer.aspx) . Ce fichier conteneur crée un writer utilisé pour sérialiser les données, non compressées, dans un flux de mémoire qui est ensuite enregistré dans un fichier. Le paramètre [**Codec.Null**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.codec.null.aspx) utilisé pour la création du lecteur indique que ces données ne sont pas compressées.

Les données sont lu à partir du fichier, puis désérialisées dans une collection d’objets. Cette collection est comparée à la liste initiale des enregistrements Avro pour confirmer qu’ils sont identiques.


    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro.Schema;
        using Microsoft.Hadoop.Avro;

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes a sample data set by using a generic record and Avro object container files.
            //Serialized data is not compressed.
            public void SerializeDeserializeUsingObjectContainersGenericRecord()
            {
                Console.WriteLine("SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES\n");

                //Path for Avro object container file
                string path = "AvroSampleGenericRecordNullCodec.avro";

                Console.WriteLine("Defining the Schema and creating Sample Data Set...");

                //Define the schema in JSON
                const string Schema = @"{
                                ""type"":""record"",
                                ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
                                ""fields"":
                                    [
                                        {
                                            ""name"":""Location"",
                                            ""type"":
                                                {
                                                    ""type"":""record"",
                                                    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
                                                    ""fields"":
                                                        [
                                                            { ""name"":""Floor"", ""type"":""int"" },
                                                            { ""name"":""Room"", ""type"":""int"" }
                                                        ]
                                                }
                                        },
                                        { ""name"":""Value"", ""type"":""bytes"" }
                                    ]
                            }";

                //Create a generic serializer based on the schema
                var serializer = AvroSerializer.CreateGeneric(Schema);
                var rootSchema = serializer.WriterSchema as RecordSchema;

                //Create a generic record to represent the data
                var testData = new List<AvroRecord>();

                dynamic expected1 = new AvroRecord(rootSchema);
                dynamic location1 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location1.Floor = 1;
                location1.Room = 243;
                expected1.Location = location1;
                expected1.Value = new byte[] { 1, 2, 3, 4, 5 };
                testData.Add(expected1);

                dynamic expected2 = new AvroRecord(rootSchema);
                dynamic location2 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location2.Floor = 1;
                location2.Room = 244;
                expected2.Location = location2;
                expected2.Value = new byte[] { 6, 7, 8, 9 };
                testData.Add(expected2);

                //Serializing and saving data to file.
                //Create a MemoryStream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Data will not be compressed (Null compression codec).
                    using (var writer = AvroContainer.CreateGenericWriter(Schema, buffer, Codec.Null))
                    {
                        using (var streamWriter = new SequentialWriter<object>(writer, 24))
                        {
                            // Serialize the data to stream by using the sequential writer
                            testData.ForEach(streamWriter.Write);
                        }
                    }

                    Console.WriteLine("Saving serialized data to file...");

                    //Save stream to file
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing the data.
                //Create a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Create a SequentialReader instance for type SensorData, which will deserialize all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    using (var reader = AvroContainer.CreateGenericReader(buffer))
                    {
                        using (var streamReader = new SequentialReader<object>(reader))
                        {
                            var results = streamReader.Objects;

                            Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                            //Finally, verify the results
                            var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = (dynamic)serialized, actual = (dynamic)deserialized });
                            int count = 1;
                            foreach (var pair in pairs)
                            {
                                bool isEqual = pair.expected.Location.Floor.Equals(pair.actual.Location.Floor);
                                isEqual = isEqual && pair.expected.Location.Room.Equals(pair.actual.Location.Room);
                                isEqual = isEqual && ((byte[])pair.expected.Value).SequenceEqual((byte[])pair.actual.Value);
                                Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
                                count++;
                            }
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }

            //
            //Helper methods
            //

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading a file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using the given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of the AvroSample class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using generic record to Avro object container file
                Sample.SerializeDeserializeUsingObjectContainersGenericRecord();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.




###<a name="sample-5-serialization-using-object-container-files-with-a-custom-compression-codec"></a>Exemple 5 : Sérialisation à l’aide des fichiers de conteneur d’objets avec un codec de compression personnalisé

Le cinquième exemple montre comment utiliser un codec de compression personnalisé pour les fichiers de Avro objet conteneur. Un échantillon contenant le code de cet exemple peut être téléchargé à partir du site [d’exemples de code Azure](http://code.msdn.microsoft.com/windowsazure/Serialize-data-with-the-67159111) .

La [Spécification de Avro](http://avro.apache.org/docs/current/spec.html#Required+Codecs) permet l’utilisation d’un codec de compression optionnelle (en plus des valeurs par défaut **Null** et **Deflate** ). Cet exemple n’implémente pas un codec novatrices telles que Snappy (indiqué sous la forme d’un codec facultatif pris en charge dans la [Spécification de Avro](http://avro.apache.org/docs/current/spec.html#snappy)). Il montre comment utiliser la mise en oeuvre de.NET Framework 4.5 de la [**Deflate**] [ deflate-110] codec, qui fournit un meilleur algorithme de compression en fonction de la bibliothèque de compression [zlib](http://zlib.net/) que la version de.NET Framework 4 par défaut.


    //
    // This code needs to be compiled with the parameter Target Framework set as ".NET Framework 4.5"
    // to ensure the desired implementation of the Deflate compression algorithm is used.
    // Ensure your C# project is set up accordingly.
    //

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.IO;
        using System.IO.Compression;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        #region Defining objects for serialization
        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }
        #endregion

        #region Defining custom codec based on .NET Framework V.4.5 Deflate
        //Avro.NET codec class contains two methods,
        //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed),
        //which are the key ones for data compression.
        //To enable a custom codec, one needs to implement these methods for the required codec.

        #region Defining Compression and Decompression Streams
        //DeflateStream (class from System.IO.Compression namespace that implements Deflate algorithm)
        //cannot be directly used for Avro because it does not support vital operations like Seek.
        //Thus one needs to implement two classes inherited from stream
        //(one for compressed and one for decompressed stream)
        //that use Deflate compression and implement all required features.
        internal sealed class CompressionStreamDeflate45 : Stream
        {
            private readonly Stream buffer;
            private DeflateStream compressionStream;

            public CompressionStreamDeflate45(Stream buffer)
            {
                Debug.Assert(buffer != null, "Buffer is not allowed to be null.");

                this.compressionStream = new DeflateStream(buffer, CompressionLevel.Fastest, true);
                this.buffer = buffer;
            }

            public override bool CanRead
            {
                get { return this.buffer.CanRead; }
            }

            public override bool CanSeek
            {
                get { return true; }
            }

            public override bool CanWrite
            {
                get { return this.buffer.CanWrite; }
            }

            public override void Flush()
            {
                this.compressionStream.Close();
            }

            public override long Length
            {
                get { return this.buffer.Length; }
            }

            public override long Position
            {
                get
                {
                    return this.buffer.Position;
                }

                set
                {
                    this.buffer.Position = value;
                }
            }

            public override int Read(byte[] buffer, int offset, int count)
            {
                return this.buffer.Read(buffer, offset, count);
            }

            public override long Seek(long offset, SeekOrigin origin)
            {
                return this.buffer.Seek(offset, origin);
            }

            public override void SetLength(long value)
            {
                throw new NotSupportedException();
            }

            public override void Write(byte[] buffer, int offset, int count)
            {
                this.compressionStream.Write(buffer, offset, count);
            }

            protected override void Dispose(bool disposed)
            {
                base.Dispose(disposed);

                if (disposed)
                {
                    this.compressionStream.Dispose();
                    this.compressionStream = null;
                }
            }
        }

        internal sealed class DecompressionStreamDeflate45 : Stream
        {
            private readonly DeflateStream decompressed;

            public DecompressionStreamDeflate45(Stream compressed)
            {
                this.decompressed = new DeflateStream(compressed, CompressionMode.Decompress, true);
            }

            public override bool CanRead
            {
                get { return true; }
            }

            public override bool CanSeek
            {
                get { return true; }
            }

            public override bool CanWrite
            {
                get { return false; }
            }

            public override void Flush()
            {
                this.decompressed.Close();
            }

            public override long Length
            {
                get { return this.decompressed.Length; }
            }

            public override long Position
            {
                get
                {
                    return this.decompressed.Position;
                }

                set
                {
                    throw new NotSupportedException();
                }
            }

            public override int Read(byte[] buffer, int offset, int count)
            {
                return this.decompressed.Read(buffer, offset, count);
            }

            public override long Seek(long offset, SeekOrigin origin)
            {
                throw new NotSupportedException();
            }

            public override void SetLength(long value)
            {
                throw new NotSupportedException();
            }

            public override void Write(byte[] buffer, int offset, int count)
            {
                throw new NotSupportedException();
            }

            protected override void Dispose(bool disposing)
            {
                base.Dispose(disposing);

                if (disposing)
                {
                    this.decompressed.Dispose();
                }
            }
        }
        #endregion

        #region Define Codec
        //Define the actual codec class containing the required methods for manipulating streams:
        //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed).
        //Codec class uses classes for compressed and decompressed streams defined above.
        internal sealed class DeflateCodec45 : Codec
        {

            //We merely use different IMPLEMENTATIONS of Deflate, so CodecName remains "deflate"
            public static readonly string CodecName = "deflate";

            public DeflateCodec45()
                : base(CodecName)
            {
            }

            public override Stream GetCompressedStreamOver(Stream decompressed)
            {
                if (decompressed == null)
                {
                    throw new ArgumentNullException("decompressed");
                }

                return new CompressionStreamDeflate45(decompressed);
            }

            public override Stream GetDecompressedStreamOver(Stream compressed)
            {
                if (compressed == null)
                {
                    throw new ArgumentNullException("compressed");
                }

                return new DecompressionStreamDeflate45(compressed);
            }
        }
        #endregion

        #region Define modified Codec Factory
        //Define modified codec factory to be used in the reader.
        //It will catch the attempt to use "Deflate" and provide  a custom codec.
        //For all other cases, it will rely on the base class (CodecFactory).
        internal sealed class CodecFactoryDeflate45 : CodecFactory
        {

            public override Codec Create(string codecName)
            {
                if (codecName == DeflateCodec45.CodecName)
                    return new DeflateCodec45();
                else
                    return base.Create(codecName);
            }
        }
        #endregion

        #endregion

        #region Sample Class with demonstration methods
        //This class contains methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes sample data set by using reflection and Avro object container files.
            //Serialized data is compressed with the custom compression codec (Deflate of .NET Framework 4.5).
            //
            //This sample uses memory stream for all operations related to serialization, deserialization and
            //object container manipulation, though file stream could be easily used.
            public void SerializeDeserializeUsingObjectContainersReflectionCustomCodec()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC\n");

                //Path for Avro object container file
                string path = "AvroSampleReflectionDeflate45.avro";

                //Create a data set by using sample class and struct
                var testData = new List<SensorData>
                        {
                            new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
                            new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
                        };

                //Serializing and saving data to file.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Here the custom codec is introduced. For convenience, the next commented code line shows how to use built-in Deflate.
                    //Note that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
                    //in read-write operations.
                    //using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
                    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, new DeflateCodec45()))
                    {
                        using (var writer = new SequentialWriter<SensorData>(w, 24))
                        {
                            // Serialize the data to stream using the sequential writer
                            testData.ForEach(writer.Write);
                        }
                    }

                    //Save stream to file
                    Console.WriteLine("Saving serialized data to file...");
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing data.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Because of SequentialReader<T> constructor signature, an AvroSerializerSettings instance is required
                    //when codec factory is explicitly specified.
                    //You may comment the line below if you want to use built-in Deflate (see next comment).
                    AvroSerializerSettings settings = new AvroSerializerSettings();

                    //Create a SequentialReader instance for type SensorData, which will deserialize all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    //Here the custom codec factory is introduced.
                    //For convenience, the next commented code line shows how to use built-in Deflate
                    //(no explicit Codec Factory parameter is required in this case).
                    //Note that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
                    //in read-write operations.
                    //using (var reader = new SequentialReader<SensorData>(AvroContainer.CreateReader<SensorData>(buffer, true)))
                    using (var reader = new SequentialReader<SensorData>(
                        AvroContainer.CreateReader<SensorData>(buffer, true, settings, new CodecFactoryDeflate45())))
                    {
                        var results = reader.Objects;

                        //Finally, verify that deserialized data matches the original one
                        Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
                        bool isEqual;
                        int count = 1;
                        var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
                        foreach (var pair in pairs)
                        {
                            isEqual = this.Equal(pair.expected, pair.actual);
                            Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
                            count++;
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }
        #endregion

            #region Helper Methods

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }
            #endregion

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using reflection to Avro object container file using custom codec
                Sample.SerializeDeserializeUsingObjectContainersReflectionCustomCodec();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    //For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.

###<a name="sample-6-using-avro-to-upload-data-for-the-microsoft-azure-hdinsight-service"></a>Exemple 6 : À l’aide de Avro pour charger les données pour le service Microsoft Azure HDInsight

Le sixième exemple illustre quelques techniques de programmation relatives à l’interaction avec le service de HDInsight d’Azure. Un échantillon contenant le code de cet exemple peut être téléchargé à partir du site [d’exemples de code Azure](https://code.msdn.microsoft.com/windowsazure/Using-Avro-to-upload-data-ae81b1e3) .

L’exemple effectue les opérations suivantes :

* Se connecte à un cluster de service HDInsight existant.
* Sérialise plusieurs fichiers CSV et télécharge le résultat pour le stockage des objets Blob Azure. (Les fichiers CSV sont distribués avec l’échantillon et représentent un extrait à partir des données historiques de AMEX Stock distribués par [Infochimps](http://www.infochimps.com/) pour la période 1970-2010. L’exemple lit les données de fichier CSV, convertit les enregistrements à des instances de la classe de **Stock** et les sérialise ensuite en utilisant la réflexion. Définition du type de stock est créée à partir d’un schéma JSON via l’utilitaire de génération de code de Microsoft Avro Library.
* Crée une nouvelle table externe appelée **Stocks** dans la ruche et les liens pour les données téléchargée à l’étape précédente.
* Exécute une requête sur la table de **Stocks** à l’aide de la ruche.

En outre, l’exemple exécute une procédure de nettoyage avant et après l’exécution des principales opérations. Pendant le nettoyage, tous les dossiers et les données Blob d’Azure associées sont supprimées et la table de la ruche est supprimée. Vous pouvez également appeler la procédure de nettoyage de l’exemple de ligne de commande.

L’exemple possède les conditions préalables suivantes :

* Un abonnement Microsoft Azure actif et son ID d’abonnement.
* Un certificat de gestion de l’abonnement avec la clé privée correspondante. Le certificat doit être installé dans le stockage privé utilisateur en cours sur l’ordinateur utilisé pour exécuter l’exemple.
* Un cluster de HDInsight actif.
* Un compte de stockage Azure lié au cluster HDInsight à partir de la précédente condition préalable, ainsi que la clé d’accès principal ou secondaire.

Toutes les informations dans les conditions préalables doivent être entrées à l’exemple de fichier de configuration avant l’exécution de l’exemple. Il existe deux manières de procéder :

* Modifiez le fichier app.config dans le répertoire racine de l’exemple et puis générer l’exemple
* Tout d’abord générer l’exemple et modifiez AvroHDISample.exe.config dans le répertoire de build

Dans les deux cas, toutes les modifications doivent être effectuées les **<appSettings>** section de paramètres. Veuillez suivre les commentaires dans le fichier.
L’exemple est exécuté à partir de la ligne de commande en exécutant la commande suivante (où le fichier .zip avec l’échantillon a été considéré comme extraits dans C:\AvroHDISample ; si sinon, utilisez le chemin d’accès du fichier concerné) :

    AvroHDISample run C:\AvroHDISample\Data

Pour nettoyer le cluster, exécutez la commande suivante :

    AvroHDISample clean

[deflate-100]: http://msdn.microsoft.com/library/system.io.compression.deflatestream(v=vs.100).aspx
[deflate-110]: http://msdn.microsoft.com/library/system.io.compression.deflatestream(v=vs.110).aspx
