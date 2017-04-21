
<!--
includes/sql-database-include-connection-string-40-config.md

Latest Freshness check:  2015-09-04 , GeneMi.

## Connection string
-->


### <a name="example-config-file-for-connection-string-security"></a>Exemple de fichier de configuration de la sécurité des chaînes de connexion


Il est incorrect pour placer la chaîne de connexion en tant que littéraux dans votre code C#. Il est préférable de placer la chaîne de connexion dans un fichier de configuration. Il vous pouvez modifier la chaîne de n’importe quel moment sans avoir à recompiler.

Supposons que votre programme C# compilé nommé **ConsoleApplication1.exe**, et que ce .exe réside dans un **bin\debug\* * répertoire.

Dans cet exemple, la plupart des composants de votre chaîne de connexion sont stockés dans un fichier de configuration nommé exactement **ConsoleApplication1.exe.config**. Ce fichier de configuration doit également résider dans **bin\debug\**.

Dans le code XML du fichier de configuration suivant pour afficher une chaîne de connexion nommée **ConnectionString4NoUserIDNoPassword**. Le code C# ressemble pour cette chaîne.

Vous devez modifier les noms réels dans les espaces réservés :

- {your_serverName_here}
- {your_databaseName_here}



        <?xml version="1.0" encoding="utf-8" ?>
        <configuration>
            <startup> 
                <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
            </startup>
        
            <connectionStrings>
                <clear />
                <add name="ConnectionString4NoUserIDNoPassword"
                providerName="System.Data.ProviderName"
        
                connectionString=
                "Server=tcp:{your_serverName_here}.database.windows.net,1433;
                Database={your_databaseName_here};
                Connection Timeout=30;
                Encrypt=True;
                TrustServerCertificate=False;" />
            </connectionStrings>
        </configuration>



Pour cet exemple, nous avons choisi d’omettre les deux paramètres :

- ID utilisateur = {your_userName_here} ;
- Mot de passe = {your_password_here} ;


Vous pouvez les inclure, mais il est parfois préférable d’avoir votre programme d’obtenir ces valeurs à partir de l’entrée au clavier par l’utilisateur. Ça dépend.



<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
