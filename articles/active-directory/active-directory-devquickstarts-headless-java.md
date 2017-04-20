<properties
    pageTitle="Azure AD Java mise en route | Microsoft Azure"
    description="Comment générer une application de ligne de commande Java qui se connecte les utilisateurs à accéder à une API."
    services="active-directory"
    documentationCenter="java"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
  ms.tgt_pltfrm="na"
    ms.devlang="java"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="brandwe"/>


# <a name="using-java-command-line-app-to-access-an-api-with-azure-ad"></a>À l’aide d’application de ligne de commande Java pour accéder à une API avec Azure AD

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

AD Azure rend simple et directe sous-traiter la gestion d’identité de votre application web, fournissant la même connexion et de déconnexion avec seulement quelques lignes de code.  Dans les applications web Java, vous pouvez faire à l’aide de l’implémentation Microsoft de la ADAL4J pilotées par des Communautés.

  Ici, nous allons utiliser ADAL4J pour :
- Connecter l’utilisateur à l’application à l’aide d’Active Directory Azure comme le fournisseur d’identité.
- Afficher des informations sur l’utilisateur.
- Connexion de l’utilisateur de l’application.

Pour ce faire, vous devez :

1. Inscrire une application avec Azure AD
2. Configurez votre application pour utiliser la bibliothèque ADAL4J.
3. La bibliothèque ADAL4J permet d’émettre des demandes de connexion et de déconnexion à AD Azure.
4. Imprimer les données relatives à l’utilisateur.

Pour démarrer, [Téléchargez le squelette d’application](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/skeleton.zip) ou [Télécharger l’exemple terminé](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect\/archive/complete.zip).  Vous aurez également besoin d’un locataire AD Azure dans lequel vous souhaitez enregistrer votre application.  Si vous n’en avez pas déjà fait, [Apprenez à en obtenir un](active-directory-howto-tenant.md).

## <a name="1--register-an-application-with-azure-ad"></a>1. inscription d’une Application avec Azure AD
Pour permettre à votre application authentifier les utilisateurs, vous devez tout d’abord enregistrer une nouvelle application dans votre client.

- Signe dans le portail de gestion Azure.
- Dans nav de la main gauche, cliquez sur **Active Directory**.
- Sélectionnez le client où vous souhaitez enregistrer l’application.
- Cliquez sur l’onglet **Applications** , puis cliquez sur Ajouter dans le bac d’alimentation inférieur.
- Suivez les invites et créer une nouvelle **Application Web et/ou WebAPI**.
    - Le **nom** de l’application décrit votre application aux utilisateurs finals
    - L' **Ouverture de session d’URL** est l’URL de base de votre application.  Valeur par défaut du squelette est `http://localhost:8080/adal4jsample/`.
    - L' **URI d’application ID** est un identificateur unique pour votre application.  La convention consiste à utiliser `https://<tenant-domain>/<app-name>`, par exemple :`http://localhost:8080/adal4jsample/`
- Une fois l’enregistrement terminé, DAS affecter à votre application un identificateur de client unique.  Vous aurez besoin de cette valeur dans les sections suivantes, donc copier à partir de l’onglet Configurer.

Une fois dans le portail de votre application créer un **Secret de l’Application** pour votre application et le copier vers le bas.  Vous en aurez besoin sous peu.


## <a name="2-set-up-your-app-to-use-adal4j-library-and-prerequisites-using-maven"></a>2. configurer votre application pour utiliser la bibliothèque de ADAL4J et les conditions préalables à l’aide de Maven
Ici, nous allons configurer ADAL4J pour utiliser le protocole d’authentification OpenID de se connecter.  ADAL4J sera utilisé pour émettre des demandes de connexion et de déconnexion, la gestion de session de l’utilisateur et obtenir des informations sur l’utilisateur, entre autres.

-   Dans le répertoire racine de votre projet, ouvrir ou créer `pom.xml` et recherchez la `// TODO: provide dependencies for Maven` et les remplacer par les éléments suivants :

```Java
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>public-client-adal4j-sample</artifactId>
    <packaging>jar</packaging>
    <version>0.0.1-SNAPSHOT</version>
    <name>public-client-adal4j-sample</name>
    <url>http://maven.apache.org</url>
    <properties>
        <spring.version>3.0.5.RELEASE</spring.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>adal4j</artifactId>
            <version>1.1.2</version>
        </dependency>
        <dependency>
            <groupId>com.nimbusds</groupId>
            <artifactId>oauth2-oidc-sdk</artifactId>
            <version>4.5</version>
        </dependency>
        <dependency>
            <groupId>org.json</groupId>
            <artifactId>json</artifactId>
            <version>20090211</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.0.1</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>1.7.5</version>
        </dependency>
</dependencies>
    <build>
        <finalName>public-client-adal4j-sample</finalName>
        <plugins>
                <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <version>1.2.1</version>
            <configuration>
                <mainClass>PublicClient</mainClass>
            </configuration>
        </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>1.7</source>
                    <target>1.7</target>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-dependency-plugin</artifactId>
                <executions>
                    <execution>
                        <id>install</id>
                        <phase>install</phase>
                        <goals>
                            <goal>sources</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-resources-plugin</artifactId>
                <version>2.5</version>
                <configuration>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
            <plugin>
        <artifactId>maven-assembly-plugin</artifactId>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>single</goal>
            </goals>
          </execution>
        </executions>
        <configuration>
          <descriptorRefs>
            <descriptorRef>jar-with-dependencies</descriptorRef>
          </descriptorRefs>
        </configuration>
      </plugin>
      <plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-assembly-plugin</artifactId>
  <configuration>
    <archive>
      <manifest>
        <mainClass>PublicClient</mainClass>
      </manifest>
    </archive>
  </configuration>
</plugin>
        </plugins>
    </build>

</project>


```



## <a name="3-create-the-java-publicclient-file"></a>3. Créez le fichier de PublicClient java

Comme indiqué ci-dessus, nous allons utiliser l’API de graphique pour obtenir des données sur l’utilisateur connecté. Pour que cela soit facile pour nous, nous devons créer un fichier pour représenter un **Objet d’annuaire** et un fichier individuel pour représenter l' **utilisateur** afin que le modèle OO de Java peut être utilisé.

1. Créez un fichier appelé `DirectoryObject.java` que nous utiliserons pour stocker les données de base sur n’importe quel DirectoryObject (vous pouvez n’hésitez pas à utiliser ultérieurement pour toutes les autres requêtes graphique vous pouvez le faire). Vous pouvez couper/coller cette ci-dessous :

```Java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

import javax.naming.ServiceUnavailableException;

import com.microsoft.aad.adal4j.AuthenticationContext;
import com.microsoft.aad.adal4j.AuthenticationResult;

public class PublicClient {

    private final static String AUTHORITY = "https://login.microsoftonline.com/common/";
    private final static String CLIENT_ID = "2a4da06c-ff07-410d-af8a-542a512f5092";

    public static void main(String args[]) throws Exception {

        try (BufferedReader br = new BufferedReader(new InputStreamReader(
                System.in))) {
            System.out.print("Enter username: ");
            String username = br.readLine();
            System.out.print("Enter password: ");
            String password = br.readLine();

            AuthenticationResult result = getAccessTokenFromUserCredentials(
                    username, password);
            System.out.println("Access Token - " + result.getAccessToken());
            System.out.println("Refresh Token - " + result.getRefreshToken());
            System.out.println("ID Token - " + result.getIdToken());
        }
    }

    private static AuthenticationResult getAccessTokenFromUserCredentials(
            String username, String password) throws Exception {
        AuthenticationContext context = null;
        AuthenticationResult result = null;
        ExecutorService service = null;
        try {
            service = Executors.newFixedThreadPool(1);
            context = new AuthenticationContext(AUTHORITY, false, service);
            Future<AuthenticationResult> future = context.acquireToken(
                    "https://graph.windows.net", CLIENT_ID, username, password,
                    null);
            result = future.get();
        } finally {
            service.shutdown();
        }

        if (result == null) {
            throw new ServiceUnavailableException(
                    "authentication result was null");
        }
        return result;
    }
}


```


##<a name="compile-and-run-the-sample"></a>Compiler et exécuter l’exemple

Modifier en arrière dans votre répertoire racine et exécutez la commande suivante pour générer l’exemple, vous placez simplement à l’aide de `maven`. Utiliser les `pom.xml` vous avez écrit pour les dépendances de fichier.

`$ mvn package`

Vous devez maintenant avoir un `adal4jsample.war` de fichiers dans votre `/targets` répertoire. Vous pouvez déployer dans votre conteneur de Tomcat et à l’adresse 

`http://localhost:8080/adal4jsample/`


> [AZURE.NOTE] 
Il est très facile de déployer une guerre avec les derniers serveurs de Tomcat. Accédez simplement à `http://localhost:8080/manager/` et suivez les instructions sur le téléchargement de votre « adal4jsample.war' fichier. Il sera autodeploy pour vous avec le point de terminaison correct.

##<a name="next-steps"></a>Étapes suivantes

Félicitations ! Vous disposez maintenant d’une application Java qui a la capacité d’authentifier les utilisateurs, en toute sécurité de travail appeler des API Web à l’aide de OAuth 2.0 et obtenir des informations de base sur l’utilisateur.  Si vous ne l’avez pas déjà, il est temps de remplir vos clients avec certains utilisateurs.

Pour référence, l’exemple complet (sans les valeurs de configuration) [est fourni sous la forme d’un fichier zip ici](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/complete.zip), ou vous pouvez le cloner à partir de GitHub :

```git clone --branch complete https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect.git```

