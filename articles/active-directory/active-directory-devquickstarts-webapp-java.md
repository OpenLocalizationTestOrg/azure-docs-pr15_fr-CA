<properties
    pageTitle="Azure AD Java mise en route | Microsoft Azure"
    description="Comment créer une application web Java qui signe les utilisateurs avec un compte professionnel ou l’école."
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


# <a name="java-web-app-sign-in--sign-out-with-azure-ad"></a>Java Web App connexion et déconnexion avec Azure AD

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


## <a name="2-set-up-your-app-to-use-adal4j-library-and-prerequisities-using-maven"></a>2. configurer votre application pour utiliser la bibliothèque de ADAL4J et de prerequisities à l’aide de Maven
Ici, nous allons configurer ADAL4J pour utiliser le protocole d’authentification OpenID de se connecter.  ADAL4J sera utilisé pour émettre des demandes de connexion et de déconnexion, la gestion de session de l’utilisateur et obtenir des informations sur l’utilisateur, entre autres.

-   Dans le répertoire racine de votre projet, ouvrir ou créer `pom.xml` et recherchez la `// TODO: provide dependencies for Maven` et les remplacer par les éléments suivants :

```Java
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>adal4jsample</artifactId>
    <packaging>war</packaging>
    <version>0.0.1-SNAPSHOT</version>
    <name>adal4jsample</name>
    <url>http://maven.apache.org</url>
    <properties>
        <spring.version>3.0.5.RELEASE</spring.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>adal4j</artifactId>
            <version>1.1.1</version>
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
        <!-- Spring 3 dependencies -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-web</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>${spring.version}</version>
        </dependency>
    </dependencies>

    <build>
        <finalName>sample-for-adal4j</finalName>
        <plugins>
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
                <artifactId>maven-war-plugin</artifactId>
                <version>2.4</version>
                <configuration>
                    <warName>${project.artifactId}</warName>
                    <source>${project.basedir}\src</source>
                    <target>${maven.compiler.target}</target>
                    <encoding>utf-8</encoding>
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
        </plugins>
    </build>

</project>

```


## <a name="3-create-the-java-web-application-files-web-inf"></a>3. créer le site web de Java (WEB-INF) fichiers d’application

Ici, nous allons configurer l’application web de Java pour utiliser le protocole d’authentification OpenID de se connecter.  La bibliothèque ADAL4J sera utilisée pour émettre des demandes de connexion et de déconnexion, la gestion de session de l’utilisateur et obtenir des informations sur l’utilisateur, entre autres.

-   Pour commencer, ouvrez le `web.xml` fichier situé sous `\webapp\WEB-INF\`et entrez les valeurs de la configuration de votre application dans le fichier xml.

Le fichier doit se présenter comme suit :

```xml
<?xml version="1.0"?>
<web-app id="WebApp_ID" version="2.4"
    xmlns="http://java.sun.com/xml/ns/j2ee" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee 
    http://java.sun.com/xml/ns/j2ee/web-app_2_4.xsd">
    <display-name>Archetype Created Web Application</display-name>
    <context-param>
        <param-name>authority</param-name>
        <param-value>https://login.windows.net/</param-value>
    </context-param>
    <context-param>
        <param-name>tenant</param-name>
        <param-value>YOUR_TENANT_NAME</param-value>
    </context-param>

    <filter>
        <filter-name>BasicFilter</filter-name>
        <filter-class>com.microsoft.aad.adal4jsample.BasicFilter</filter-class>
        <init-param>
            <param-name>client_id</param-name>
            <param-value>YOUR_CLIENT_ID</param-value>
        </init-param>
        <init-param>
            <param-name>secret_key</param-name>
            <param-value>YOUR_CLIENT_SECRET</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>BasicFilter</filter-name>
        <url-pattern>/secure/*</url-pattern>
    </filter-mapping>

    <servlet>
        <servlet-name>mvc-dispatcher</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>mvc-dispatcher</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>/WEB-INF/mvc-dispatcher-servlet.xml</param-value>
    </context-param>

    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
</web-app>

```


    -   Le `YOUR_CLIENT_ID` est l' **Id de l’Application** affectée à votre application dans le portail de l’enregistrement.
    -   Le `YOUR_CLIENT_SECRET` est le **Secret de l’Application** que vous avez créée dans le portail.
    - Le `YOUR_TENANT_NAME` est le **nom du client** de votre application, par exemple : contoso.onmicrosoft.com

Laissez les autres paramètres de configuration uniquement.

> [AZURE.NOTE]
Comme vous pouvez le voir dans le fichier XML, nous écrivons une webapp JSP/Servlet appelé `mvc-dispatcher` qui utilisera le `BasicFilter` à chaque fois que nous visiter le / URL sécurisée. Vous verrez dans le reste de la même nous écrire que nous allons utiliser / sécurisé comme un lieu de résidence de notre contenu protégé et va forcer l’authentification pour Azure Active Directory.

-   Ensuite, créez les `mvc-dispatcher-servlet.xml` fichier situé sous `\webapp\WEB-INF\`et entrez les informations suivantes :

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:context="http://www.springframework.org/schema/context"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans     
        http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
        http://www.springframework.org/schema/context 
        http://www.springframework.org/schema/context/spring-context-3.0.xsd">

    <context:component-scan base-package="com.microsoft.aad.adal4jsample" />

    <bean
        class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix">
            <value>/</value>
        </property>
        <property name="suffix">
            <value>.jsp</value>
        </property>
    </bean>

</beans>
```

Cela indiquera la webapp ressort et où trouver notre fichier .jsp qui nous développerons ci-dessous.

## <a name="4-create-the-java-jsp-view-files-for-basicfilter-mvc"></a>4. création de fichiers de vue de JSP Java (pour MVC de le BasicFilter)

Nous sommes seulement la moitié a pêché avec notre installation de notre webapp dans WEB-INF. Nous devrons ensuite créer les fichiers Java Server Pages qui exécutera notre webapp qui nous a donc suggéré de dans notre configuration.

Si vous vous souvenez, nous dit Java dans notre fichier xml des fichiers de configuration qui nous ont une `/` ressource qui doit charger les fichiers .jsp, et un `/secure` les ressources qui doivent passer à travers un filtre que nous avons appelé `BasicFilter`. 

Nous allons en faire ceux maintenant.

-   Pour commencer, vous devez créer le `index.jsp` fichier situé sous `\webapp\`et couper/coller les éléments suivants :

```jsp
<html>
<body>
    <h2>Hello World!</h2>
    <ul>
    <li><a href="secure/aad">Secure Page</a></li>
    </ul>
</body>
</html>

```

Il redirige simplement vers une page sécurisée est protégée par notre filtre.

- Ensuite, dans la même permet de répertoire créer un `error.jsp` fichier pour détecter les erreurs susceptibles de se produire :

```jsp
<html>
<body>
    <h2>ERROR PAGE!</h2>
    <p>
        Exception -
        <%=request.getAttribute("error")%></p>
    <ul>
        <li><a href="<%=request.getContextPath()%>/index.jsp">Go Home</a></li>
    </ul>
</body>
</html>
```

- Enfin, nous allons en créer cette page Web sécurisée que nous voulons en créant un dossier sous `\webapp` appelée `\secure` afin que le répertoire maintenant, nous `\webapp\secure`. 

- À l’intérieur de ce répertoire, puis créons une `aad.jsp` de fichiers et de couper/coller les éléments suivants :

```jsp
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>AAD Secure Page</title>
</head>
<body>

    <h1>Directory - Users List</h1>
    <p>${users}</p>

    <ul>
        <li><a href="<%=request.getContextPath()%>/secure/aad?cc=1">Get
                new Access Token via Client Credentials</a></li>
    </ul>
    <ul>
        <li><a href="<%=request.getContextPath()%>/secure/aad?refresh=1">Get
                new Access Token via Refresh Token</a></li>
    </ul>
    <ul>
        <li><a href="<%=request.getContextPath()%>/index.jsp">Go Home</a></li>
    </ul>
</body>
</html>
```

Vous voyez que cette page redirige vers des demandes spécifiques qui notre servlet BasicFilter lire et ensuite l’exécuter sur l’utilisation de la `ADAJ4J` bibliothèque. Plutôt simple, non ?

Bien sûr, nous devons devons maintenant configurer nos fichiers Java servlet permettre d’effectuer son travail.

## <a name="5-create-some-java-helper-files-for-basicfilter-mvc"></a>5. créer des fichiers d’application d’assistance de Java (pour MVC de le BasicFilter)

Notre objectif est de créer quelques fichiers Java qui seront :

1. Permettre de connexion et de déconnexion de l’utilisateur
2. Obtenir des données sur l’utilisateur.

> [AZURE.NOTE] 
Pour obtenir des données sur l’utilisateur, nous devons utiliser l’API graphique d’Azure Active Directory. L’API de graphique est un service Web sécurisé que vous pouvez utiliser pour récupérer des données sur votre organisation, y compris des utilisateurs individuels. C’est mieux que le remplissage préalable des données sensibles dans des jetons car il garantit l’utilisateur demandant des données est autorisé et toute personne qui peut se passer pour récupérer le jeton (à partir d’un jailbroken téléphone ou web navigateur cache sur un ordinateur de bureau) ne sont pas obtenir quelques détails importants sur l’utilisateur ou de l’organisation.

Nous allons écrire certains fichiers Java pour faire ce travail pour nous :

1. Créez un dossier dans le répertoire racine nommé « adal4jsample » pour stocker tous nos fichiers java. 

Nous allons utiliser l’espace de noms `com.microsoft.aad.adal4jsample` dans nos fichiers java. La plupart des IDE crée une structure de dossiers imbriqués pour ce (par exemple, `/com/microsoft/aad/adal4jsample`). Vous êtes libre de le faire, mais il n’est pas nécessaire.

2. À l’intérieur de ce dossier, créez un fichier appelé `JSONHelper.java` que nous utiliserons pour nous aider à analyser à partir des données JSON à partir de ses jetons. Vous pouvez couper/coller cette ci-dessous :

```Java

package com.microsoft.aad.adal4jsample;

import java.lang.reflect.Field;
import java.util.Arrays;
import java.util.Enumeration;
import java.util.List;

import javax.servlet.http.HttpServletRequest;

import org.apache.commons.lang3.text.WordUtils;
import org.apache.log4j.Logger;
import org.json.JSONArray;
import org.json.JSONException;
import org.json.JSONObject;

/**
 * This class provides the methods to parse JSON Data from a JSON Formatted
 * String.
 * 
 * @author Azure Active Directory Contributor
 * 
 */
public class JSONHelper {

    private static Logger logger = Logger.getLogger(JSONHelper.class);

    JSONHelper() {
        // PropertyConfigurator.configure("log4j.properties");
    }

    /**
     * This method parses an JSON Array out of a collection of JSON Objects
     * within a string.
     * 
     * @param jSonData
     *            The JSON String that holds the collection.
     * @return An JSON Array that would contains all the collection object.
     * @throws Exception
     */
    public static JSONArray fetchDirectoryObjectJSONArray(JSONObject jsonObject) throws Exception {
        JSONArray jsonArray = new JSONArray();
        jsonArray = jsonObject.optJSONObject("responseMsg").optJSONArray("value");
        return jsonArray;
    }

    /**
     * This method parses an JSON Object out of a collection of JSON Objects
     * within a string
     * 
     * @param jsonObject
     * @return An JSON Object that would contains the DirectoryObject.
     * @throws Exception
     */
    public static JSONObject fetchDirectoryObjectJSONObject(JSONObject jsonObject) throws Exception {
        JSONObject jObj = new JSONObject();
        jObj = jsonObject.optJSONObject("responseMsg");
        return jObj;
    }

    /**
     * This method parses the skip token from a json formatted string.
     * 
     * @param jsonData
     *            The JSON Formatted String.
     * @return The skipToken.
     * @throws Exception
     */
    public static String fetchNextSkiptoken(JSONObject jsonObject) throws Exception {
        String skipToken = "";
        // Parse the skip token out of the string.
        skipToken = jsonObject.optJSONObject("responseMsg").optString("odata.nextLink");

        if (!skipToken.equalsIgnoreCase("")) {
            // Remove the unnecessary prefix from the skip token.
            int index = skipToken.indexOf("$skiptoken=") + (new String("$skiptoken=")).length();
            skipToken = skipToken.substring(index);
        }
        return skipToken;
    }

    /**
     * @param jsonObject
     * @return
     * @throws Exception
     */
    public static String fetchDeltaLink(JSONObject jsonObject) throws Exception {
        String deltaLink = "";
        // Parse the skip token out of the string.
        deltaLink = jsonObject.optJSONObject("responseMsg").optString("aad.deltaLink");
        if (deltaLink == null || deltaLink.length() == 0) {
            deltaLink = jsonObject.optJSONObject("responseMsg").optString("aad.nextLink");
            logger.info("deltaLink empty, nextLink ->" + deltaLink);

        }
        if (!deltaLink.equalsIgnoreCase("")) {
            // Remove the unnecessary prefix from the skip token.
            int index = deltaLink.indexOf("deltaLink=") + (new String("deltaLink=")).length();
            deltaLink = deltaLink.substring(index);
        }
        return deltaLink;
    }

    /**
     * This method would create a string consisting of a JSON document with all
     * the necessary elements set from the HttpServletRequest request.
     * 
     * @param request
     *            The HttpServletRequest
     * @return the string containing the JSON document.
     * @throws Exception
     *             If there is any error processing the request.
     */
    public static String createJSONString(HttpServletRequest request, String controller) throws Exception {
        JSONObject obj = new JSONObject();
        try {
            Field[] allFields = Class.forName(
                    "com.microsoft.windowsazure.activedirectory.sdk.graph.models." + controller).getDeclaredFields();
            String[] allFieldStr = new String[allFields.length];
            for (int i = 0; i < allFields.length; i++) {
                allFieldStr[i] = allFields[i].getName();
            }
            List<String> allFieldStringList = Arrays.asList(allFieldStr);
            Enumeration<String> fields = request.getParameterNames();

            while (fields.hasMoreElements()) {

                String fieldName = fields.nextElement();
                String param = request.getParameter(fieldName);
                if (allFieldStringList.contains(fieldName)) {
                    if (param == null || param.length() == 0) {
                        if (!fieldName.equalsIgnoreCase("password")) {
                            obj.put(fieldName, JSONObject.NULL);
                        }
                    } else {
                        if (fieldName.equalsIgnoreCase("password")) {
                            obj.put("passwordProfile", new JSONObject("{\"password\": \"" + param + "\"}"));
                        } else {
                            obj.put(fieldName, param);

                        }
                    }
                }
            }
        } catch (JSONException e) {
            e.printStackTrace();
        } catch (SecurityException e) {
            e.printStackTrace();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
        return obj.toString();
    }

    /**
     * 
     * @param key
     * @param value
     * @return string format of this JSON obje
     * @throws Exception
     */
    public static String createJSONString(String key, String value) throws Exception {

        JSONObject obj = new JSONObject();
        try {
            obj.put(key, value);
        } catch (JSONException e) {
            e.printStackTrace();
        }

        return obj.toString();
    }

    /**
     * This is a generic method that copies the simple attribute values from an
     * argument jsonObject to an argument generic object.
     * 
     * @param jsonObject
     *            The jsonObject from where the attributes are to be copied.
     * @param destObject
     *            The object where the attributes should be copied into.
     * @throws Exception
     *             Throws a Exception when the operation are unsuccessful.
     */
    public static <T> void convertJSONObjectToDirectoryObject(JSONObject jsonObject, T destObject) throws Exception {

        // Get the list of all the field names.
        Field[] fieldList = destObject.getClass().getDeclaredFields();

        // For all the declared field.
        for (int i = 0; i < fieldList.length; i++) {
            // If the field is of type String, that is
            // if it is a simple attribute.
            if (fieldList[i].getType().equals(String.class)) {
                // Invoke the corresponding set method of the destObject using
                // the argument taken from the jsonObject.
                destObject
                        .getClass()
                        .getMethod(String.format("set%s", WordUtils.capitalize(fieldList[i].getName())),
                                new Class[] { String.class })
                        .invoke(destObject, new Object[] { jsonObject.optString(fieldList[i].getName()) });
            }
        }
    }

    public static JSONArray joinJSONArrays(JSONArray a, JSONArray b) {
        JSONArray comb = new JSONArray();
        for (int i = 0; i < a.length(); i++) {
            comb.put(a.optJSONObject(i));
        }
        for (int i = 0; i < b.length(); i++) {
            comb.put(b.optJSONObject(i));
        }
        return comb;
    }

}

```

3. Ensuite, créez un fichier appelé `HttpClientHelper.java` que nous utiliserons pour nous aider à analyser des données HTTP à partir de notre point de terminaison DAS. Vous pouvez couper/coller cette ci-dessous :

```Java

package com.microsoft.aad.adal4jsample;

import java.io.BufferedReader;
import java.io.ByteArrayOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.net.HttpURLConnection;

import org.json.JSONException;
import org.json.JSONObject;

/**
 * This is Helper class for all RestClient class.
 * 
 * @author Azure Active Directory Contributor
 * 
 */
public class HttpClientHelper {

    public HttpClientHelper() {
        super();
    }

    public static String getResponseStringFromConn(HttpURLConnection conn, boolean isSuccess) throws IOException {

        BufferedReader reader = null;
        if (isSuccess) {
            reader = new BufferedReader(new InputStreamReader(conn.getInputStream()));
        } else {
            reader = new BufferedReader(new InputStreamReader(conn.getErrorStream()));
        }
        StringBuffer stringBuffer = new StringBuffer();
        String line = "";
        while ((line = reader.readLine()) != null) {
            stringBuffer.append(line);
        }

        return stringBuffer.toString();
    }

    public static String getResponseStringFromConn(HttpURLConnection conn, String payLoad) throws IOException {

        // Send the http message payload to the server.
        if (payLoad != null) {
            conn.setDoOutput(true);
            OutputStreamWriter osw = new OutputStreamWriter(conn.getOutputStream());
            osw.write(payLoad);
            osw.flush();
            osw.close();
        }

        // Get the message response from the server.
        BufferedReader br = new BufferedReader(new InputStreamReader(conn.getInputStream()));
        String line = "";
        StringBuffer stringBuffer = new StringBuffer();
        while ((line = br.readLine()) != null) {
            stringBuffer.append(line);
        }

        br.close();

        return stringBuffer.toString();
    }

    public static byte[] getByteaArrayFromConn(HttpURLConnection conn, boolean isSuccess) throws IOException {

        InputStream is = conn.getInputStream();
        ByteArrayOutputStream baos = new ByteArrayOutputStream();
        byte[] buff = new byte[1024];
        int bytesRead = 0;

        while ((bytesRead = is.read(buff, 0, buff.length)) != -1) {
            baos.write(buff, 0, bytesRead);
        }

        byte[] bytes = baos.toByteArray();
        baos.close();
        return bytes;
    }

    /**
     * for bad response, whose responseCode is not 200 level
     * 
     * @param responseCode
     * @param errorCode
     * @param errorMsg
     * @return
     * @throws JSONException
     */
    public static JSONObject processResponse(int responseCode, String errorCode, String errorMsg) throws JSONException {
        JSONObject response = new JSONObject();
        response.put("responseCode", responseCode);
        response.put("errorCode", errorCode);
        response.put("errorMsg", errorMsg);

        return response;
    }

    /**
     * for bad response, whose responseCode is not 200 level
     * 
     * @param responseCode
     * @param errorCode
     * @param errorMsg
     * @return
     * @throws JSONException
     */
    public static JSONObject processGoodRespStr(int responseCode, String goodRespStr) throws JSONException {
        JSONObject response = new JSONObject();
        response.put("responseCode", responseCode);
        if (goodRespStr.equalsIgnoreCase("")) {
            response.put("responseMsg", "");
        } else {
            response.put("responseMsg", new JSONObject(goodRespStr));
        }

        return response;
    }

    /**
     * for good response
     * 
     * @param responseCode
     * @param responseMsg
     * @return
     * @throws JSONException
     */
    public static JSONObject processBadRespStr(int responseCode, String responseMsg) throws JSONException {

        JSONObject response = new JSONObject();
        response.put("responseCode", responseCode);
        if (responseMsg.equalsIgnoreCase("")) { // good response is empty string
            response.put("responseMsg", "");
        } else { // bad response is json string
            JSONObject errorObject = new JSONObject(responseMsg).optJSONObject("odata.error");

            String errorCode = errorObject.optString("code");
            String errorMsg = errorObject.optJSONObject("message").optString("value");
            response.put("responseCode", responseCode);
            response.put("errorCode", errorCode);
            response.put("errorMsg", errorMsg);
        }

        return response;
    }

}

```

## <a name="6-create-the-java-graph-api-model-files-for-basicfilter-mvc"></a>6. créer le java fichiers de modèle de graphique API (pour MVC de le BasicFilter)

Comme indiqué ci-dessus, nous allons utiliser l’API de graphique pour obtenir des données sur l’utilisateur connecté. Pour que cela soit facile pour nous, nous devons créer un fichier pour représenter un **Objet d’annuaire** et un fichier individuel pour représenter l' **utilisateur** afin que le modèle OO de Java peut être utilisé.

1. Créez un fichier appelé `DirectoryObject.java` que nous utiliserons pour stocker les données de base sur n’importe quel DirectoryObject (vous pouvez n’hésitez pas à utiliser ultérieurement pour toutes les autres requêtes graphique vous pouvez le faire). Vous pouvez couper/coller cette ci-dessous :

```Java

package com.microsoft.aad.adal4jsample;

/**
 * @author Azure Active Directory Contributor
 *
 */
public abstract class DirectoryObject {
    
    public DirectoryObject() {
        super();
    }
    
    /**
     * 
     * @return
     */
    public abstract String getObjectId();
    
    /**
     * @param objectId
     */
    public abstract void setObjectId(String objectId);

    /**
     * 
     * @return
     */
    public abstract String getObjectType();

    /**
     * 
     * @param objectType
     */
    public abstract void setObjectType(String objectType);
    
    /**
     * 
     * @return
     */
    public abstract String getDisplayName();

    /**
     * 
     * @param displayName
     */
    public abstract void setDisplayName(String displayName);

}

```

2. Créez un fichier appelé `User.java` que nous utiliserons pour stocker les données de base sur n’importe quel utilisateur à partir du répertoire. Là encore, cela étant getters/setters élémentaire pour les données de l’annuaire vous pouvez couper/coller cette ci-dessous :

```Java

package com.microsoft.aad.adal4jsample;

import java.security.acl.Group;
import java.util.ArrayList;

import javax.xml.bind.annotation.XmlRootElement;

import org.json.JSONObject;

/**
 *  The User Class holds together all the members of a WAAD User entity and all the access methods and set methods
 *  @author Azure Active Directory Contributor
 */
@XmlRootElement
public class User extends DirectoryObject{
    
    // The following are the individual private members of a User object that holds
    // a particular simple attribute of an User object.
    protected String objectId;
    protected String objectType;
    protected String accountEnabled;
    protected String city;
    protected String country;
    protected String department;
    protected String dirSyncEnabled;
    protected String displayName;
    protected String facsimileTelephoneNumber;
    protected String givenName;
    protected String jobTitle;
    protected String lastDirSyncTime;
    protected String mail;
    protected String mailNickname;
    protected String mobile;
    protected String password;
    protected String passwordPolicies;
    protected String physicalDeliveryOfficeName;
    protected String postalCode;
    protected String preferredLanguage;
    protected String state;
    protected String streetAddress;
    protected String surname;
    protected String telephoneNumber;
    protected String usageLocation;
    protected String userPrincipalName;
    protected boolean isDeleted;  // this will move to dto

    /**
     * below 4 properties are for future use
     */
    // managerDisplayname of this user
    protected String managerDisplayname;
    
    // The directReports holds a list of directReports
    private ArrayList<User> directReports;
    
    // The groups holds a list of group entity this user belongs to. 
    private ArrayList<Group> groups;
    
    // The roles holds a list of role entity this user belongs to. 
    private ArrayList<Group> roles;
    
    
    /**
     * The constructor for the User class. Initializes the dynamic lists and managerDisplayname variables.
     */
    public User(){
        directReports = null;
        groups = new ArrayList<Group>();
        roles = new ArrayList<Group>();
        managerDisplayname = null;
    }
//  
//  public User(String displayName, String objectId){
//      setDisplayName(displayName);
//      setObjectId(objectId);
//  }
//  
//  public User(String displayName, String objectId, String userPrincipalName, String accountEnabled){
//      setDisplayName(displayName);
//      setObjectId(objectId);
//      setUserPrincipalName(userPrincipalName);
//      setAccountEnabled(accountEnabled);
//  }
//  

    /**
     * @return The objectId of this user.
     */
    public String getObjectId() {
        return objectId;
    }
    
    /**
     * @param objectId The objectId to set to this User object.
     */
    public void setObjectId(String objectId) {
        this.objectId = objectId;
    }


    /**
     * @return The objectType of this User.
     */
    public String getObjectType() {
        return objectType;
    }

    /**
     * @param objectType The objectType to set to this User object.
     */
    public void setObjectType(String objectType) {
        this.objectType = objectType;
    }

    /**
     * @return The userPrincipalName of this User.
     */
    public String getUserPrincipalName() {
        return userPrincipalName;
    }

    /**
     * @param userPrincipalName The userPrincipalName to set to this User object.
     */
    public void setUserPrincipalName(String userPrincipalName) {
        this.userPrincipalName = userPrincipalName;
    }

    
    /**
     * @return The usageLocation of this User.
     */
    public String getUsageLocation() {
        return usageLocation;
    }

    /**
     * @param usageLocation The usageLocation to set to this User object.
     */
    public void setUsageLocation(String usageLocation) {
        this.usageLocation = usageLocation;
    }

    /**
     * @return The telephoneNumber of this User.
     */
    public String getTelephoneNumber() {
        return telephoneNumber;
    }

    /**
     * @param telephoneNumber The telephoneNumber to set to this User object.
     */
    public void setTelephoneNumber(String telephoneNumber) {
        this.telephoneNumber = telephoneNumber;
    }

    /**
     * @return The surname of this User.
     */
    public String getSurname() {
        return surname;
    }

    /**
     * @param surname The surname to set to this User Object.
     */
    public void setSurname(String surname) {
        this.surname = surname;
    }

    /**
     * @return The streetAddress of this User.
     */
    public String getStreetAddress() {
        return streetAddress;
    }

    /**
     * @param streetAddress The streetAddress to set to this User.
     */
    public void setStreetAddress(String streetAddress) {
        this.streetAddress = streetAddress;
    }

    /**
     * @return The state of this User.
     */
    public String getState() {
        return state;
    }

    /**
     * @param state The state to set to this User object.
     */
    public void setState(String state) {
        this.state = state;
    }

    /**
     * @return The preferredLanguage of this User.
     */
    public String getPreferredLanguage() {
        return preferredLanguage;
    }

    /**
     * @param preferredLanguage The preferredLanguage to set to this User.
     */
    public void setPreferredLanguage(String preferredLanguage) {
        this.preferredLanguage = preferredLanguage;
    }

    /**
     * @return The postalCode of this User.
     */
    public String getPostalCode() {
        return postalCode;
    }

    /**
     * @param postalCode The postalCode to set to this User.
     */
    public void setPostalCode(String postalCode) {
        this.postalCode = postalCode;
    }

    /**
     * @return The physicalDeliveryOfficeName of this User.
     */
    public String getPhysicalDeliveryOfficeName() {
        return physicalDeliveryOfficeName;
    }

    /**
     * @param physicalDeliveryOfficeName The physicalDeliveryOfficeName to set to this User Object.
     */
    public void setPhysicalDeliveryOfficeName(String physicalDeliveryOfficeName) {
        this.physicalDeliveryOfficeName = physicalDeliveryOfficeName;
    }

    /**
     * @return The passwordPolicies of this User.
     */
    public String getPasswordPolicies() {
        return passwordPolicies;
    }

    /**
     * @param passwordPolicies The passwordPolicies to set to this User object.
     */
    public void setPasswordPolicies(String passwordPolicies) {
        this.passwordPolicies = passwordPolicies;
    }

    /**
     * @return The mobile of this User.
     */
    public String getMobile() {
        return mobile;
    }

    /**
     * @param mobile The mobile to set to this User object.
     */
    public void setMobile(String mobile) {
        this.mobile = mobile;
    }
    
    /**
     * @return The Password of this User.
     */
    public String getPassword() {
        return password;
    }

    /**
     * @param password The mobile to set to this User object.
     */
    public void setPassword(String password) {
        this.password = password;
    }

    /**
     * @return The mail of this User.
     */
    public String getMail() {
        return mail;
    }

    /**
     * @param mail The mail to set to this User object.
     */
    public void setMail(String mail) {
        this.mail = mail;
    }
    
    /**
     * @return The MailNickname of this User.
     */
    public String getMailNickname() {
        return mailNickname;
    }

    /**
     * @param mail The MailNickname to set to this User object.
     */
    public void setMailNickname(String mailNickname) {
        this.mailNickname = mailNickname;
    }


    /**
     * @return The jobTitle of this User.
     */
    public String getJobTitle() {
        return jobTitle;
    }

    /**
     * @param jobTitle The jobTitle to set to this User Object.
     */
    public void setJobTitle(String jobTitle) {
        this.jobTitle = jobTitle;
    }

    /**
     * @return The givenName of this User.
     */
    public String getGivenName() {
        return givenName;
    }

    /**
     * @param givenName The givenName to set to this User.
     */
    public void setGivenName(String givenName) {
        this.givenName = givenName;
    }

    /**
     * @return The facsimileTelephoneNumber of this User.
     */
    public String getFacsimileTelephoneNumber() {
        return facsimileTelephoneNumber;
    }

    /**
     * @param facsimileTelephoneNumber The facsimileTelephoneNumber to set to this User Object.
     */
    public void setFacsimileTelephoneNumber(String facsimileTelephoneNumber) {
        this.facsimileTelephoneNumber = facsimileTelephoneNumber;
    }

    /**
     * @return The displayName of this User.
     */
    public String getDisplayName() {
        return displayName;
    }

    /**
     * @param displayName The displayName to set to this User Object.
     */
    public void setDisplayName(String displayName) {
        this.displayName = displayName;
    }

    /**
     * @return The dirSyncEnabled of this User.
     */
    public String getDirSyncEnabled() {
        return dirSyncEnabled;
    }

    /**
     * @param dirSyncEnabled The dirSyncEnabled to set to this User.
     */
    public void setDirSyncEnabled(String dirSyncEnabled) {
        this.dirSyncEnabled = dirSyncEnabled;
    }

    /**
     * @return The department of this User.
     */
    public String getDepartment() {
        return department;
    }

    /**
     * @param department The department to set to this User.
     */
    public void setDepartment(String department) {
        this.department = department;
    }

    /**
     * @return The lastDirSyncTime of this User.
     */
    public String getLastDirSyncTime() {
        return lastDirSyncTime;
    }

    /**
     * @param lastDirSyncTime The lastDirSyncTime to set to this User.
     */
    public void setLastDirSyncTime(String lastDirSyncTime) {
        this.lastDirSyncTime = lastDirSyncTime;
    }

    /**
     * @return The country of this User.
     */
    public String getCountry() {
        return country;
    }

    /**
     * @param country The country to set to this User.
     */
    public void setCountry(String country) {
        this.country = country;
    }

    /**
     * @return The city of this User.
     */
    public String getCity() {
        return city;
    }

    /**
     * @param city The city to set to this User.
     */
    public void setCity(String city) {
        this.city = city;
    }

    /**
     * @return The accountEnabled attribute of this User.
     */
    public String getAccountEnabled() {
        return accountEnabled;
    }

    /**
     * @param accountEnabled The accountEnabled to set to this User.
     */
    public void setAccountEnabled(String accountEnabled) {
        this.accountEnabled = accountEnabled;
    }
    
    public boolean isIsDeleted() {
        return this.isDeleted;
    }

    public void setIsDeleted(boolean isDeleted) {
        this.isDeleted = isDeleted;
    }

    @Override
    public String toString() {
        return new JSONObject(this).toString();
    }
    
    public String getManagerDisplayname(){
        return managerDisplayname;
    }
    
    public void setManagerDisplayname(String managerDisplayname){
        this.managerDisplayname = managerDisplayname;
    }
}


/**
 * The Class DirectReports Holds the essential data for a single DirectReport entry. Namely,
 * it holds the displayName and the objectId of the direct entry. Furthermore, it provides the
 * access methods to set or get the displayName and the ObjectId of this entry.
 */
//class DirectReport extends User{
//
//  private String displayName;
//  private String objectId;
//   
//  /**
//   * Two arguments Constructor for the DirectReport Class.
//   * @param displayName
//   * @param objectId
//   */
//  public DirectReport(String displayName, String objectId){
//      this.displayName = displayName;
//      this.objectId = objectId;
//  }
//
//  /**
//   * @return The diaplayName of this direct report entry.
//   */
//  public String getDisplayName() {
//      return displayName;
//  }
//
//  
//  /**
//   *  @return The objectId of this direct report entry. 
//   */
//  public String getObjectId() {
//      return objectId;
//  }
//
//}

```

## <a name="7-create-the-authentication-modelcontroller-files-for-basicfilter"></a>7. Créez les fichiers de modèle d’authentification par contrôleur (pour BasicFilter)

Oui, Java est assez clair, mais nous avons presque terminé. Last, avant de nous écrire le BasicFilter pour servlet pour gérer nos demandes, nous allons écrire certaines d’assistance plus de fichiers que la `ADAL4J` a besoin de bibliothèque. 

1. Créez un fichier appelé `AuthHelper.java` qui nous permettra de proposer les méthodes que nous utiliserons pour déterminer l’état de l’utilisation de connecté. Celles-ci comprennent :

- `isAuthenticated()`méthode qui retourne si l’utilisateur est connecté ou non
- `containsAuthenticationData()`qui indique si le jeton comporte des données ou non
- `isAuthenticationSuccessful()`qui indique si l’authentification a réussi pour l’utilisateur.

Couper-coller le code ci-dessous :

```Java
package com.microsoft.aad.adal4jsample;

import java.util.Map;

import javax.servlet.http.HttpServletRequest;

import com.microsoft.aad.adal4j.AuthenticationResult;
import com.nimbusds.openid.connect.sdk.AuthenticationResponse;
import com.nimbusds.openid.connect.sdk.AuthenticationResponseParser;
import com.nimbusds.openid.connect.sdk.AuthenticationSuccessResponse;

public final class AuthHelper {

    public static final String PRINCIPAL_SESSION_NAME = "principal";

    private AuthHelper() {
    }

    public static boolean isAuthenticated(HttpServletRequest request) {
        return request.getSession().getAttribute(PRINCIPAL_SESSION_NAME) != null;
    }

    public static AuthenticationResult getAuthSessionObject(
            HttpServletRequest request) {
        return (AuthenticationResult) request.getSession().getAttribute(
                PRINCIPAL_SESSION_NAME);
    }

    public static boolean containsAuthenticationData(
            HttpServletRequest httpRequest) {
        Map<String, String[]> map = httpRequest.getParameterMap();
        return httpRequest.getMethod().equalsIgnoreCase("POST") && (httpRequest.getParameterMap().containsKey(
                        AuthParameterNames.ERROR)
                        || httpRequest.getParameterMap().containsKey(
                                AuthParameterNames.ID_TOKEN) || httpRequest
                        .getParameterMap().containsKey(AuthParameterNames.CODE));
    }

    public static boolean isAuthenticationSuccessful(
            AuthenticationResponse authResponse) {
        return authResponse instanceof AuthenticationSuccessResponse;
    }
}
```

2. Créez un fichier appelé `AuthParameterNames.java` qui nous permettra de proposer des variables immuables `ADAL4J` nécessitera. Couper/pate suivantes :

```Java
package com.microsoft.aad.adal4jsample;

public final class AuthParameterNames {

    private AuthParameterNames() {
    }

    public static String ERROR = "error";
    public static String ERROR_DESCRIPTION = "error_description";
    public static String ERROR_URI = "error_uri";
    public static String ID_TOKEN = "id_token";
    public static String CODE = "code";
}
```

3. Enfin, créez un fichier appelé `AadController.java` qui est le contrôleur de notre modèle MVC qui nous faire part de notre contrôleur JSP et exposer les `secure/aad` point de terminaison URL de notre application. En outre, nous les avons la requête graphique dans ce fichier.

Couper-coller le texte suivante :

```Java
package com.microsoft.aad.adal4jsample;

import java.net.HttpURLConnection;
import java.net.URL;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpSession;

import org.json.JSONArray;
import org.json.JSONObject;
import org.springframework.stereotype.Controller;
import org.springframework.ui.ModelMap;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

import com.microsoft.aad.adal4j.AuthenticationResult;

@Controller
@RequestMapping("/secure/aad")
public class AadController {

    @RequestMapping(method = { RequestMethod.GET, RequestMethod.POST })
    public String getDirectoryObjects(ModelMap model, HttpServletRequest httpRequest) {
        HttpSession session = httpRequest.getSession();
        AuthenticationResult result = (AuthenticationResult) session.getAttribute(AuthHelper.PRINCIPAL_SESSION_NAME);
        if (result == null) {
            model.addAttribute("error", new Exception("AuthenticationResult not found in session."));
            return "/error";
        } else {
            String data;
            try {
                data = this.getUsernamesFromGraph(result.getAccessToken(), session.getServletContext()
                        .getInitParameter("tenant"));
                model.addAttribute("users", data);
            } catch (Exception e) {
                model.addAttribute("error", e);
                return "/error";
            }
        }
        return "/secure/aad";
    }

    private String getUsernamesFromGraph(String accessToken, String tenant) throws Exception {
        URL url = new URL(String.format("https://graph.windows.net/%s/users?api-version=2013-04-05", tenant,
                accessToken));

        HttpURLConnection conn = (HttpURLConnection) url.openConnection();
        // Set the appropriate header fields in the request header.
        conn.setRequestProperty("api-version", "2013-04-05");
        conn.setRequestProperty("Authorization", accessToken);
        conn.setRequestProperty("Accept", "application/json;odata=minimalmetadata");
        String goodRespStr = HttpClientHelper.getResponseStringFromConn(conn, true);
        // logger.info("goodRespStr ->" + goodRespStr);
        int responseCode = conn.getResponseCode();
        JSONObject response = HttpClientHelper.processGoodRespStr(responseCode, goodRespStr);
        JSONArray users = new JSONArray();

        users = JSONHelper.fetchDirectoryObjectJSONArray(response);

        StringBuilder builder = new StringBuilder();
        User user = null;
        for (int i = 0; i < users.length(); i++) {
            JSONObject thisUserJSONObject = users.optJSONObject(i);
            user = new User();
            JSONHelper.convertJSONObjectToDirectoryObject(thisUserJSONObject, user);
            builder.append(user.getUserPrincipalName() + "<br/>");
        }
        return builder.toString();
    }

}

```

## <a name="8-create-the-basicfilter-file-for-basicfilter-mvc"></a>8. créer le fichier BasicFilter (pour MVC de le BasicFilter)

Nous sommes enfin prêts à créer le fichier BasicFilter pour gérer les demandes à partir de notre vue (fichiers JSP).

Créez un fichier appelé `BasicFilter.java` qui contient les éléments suivants :

```Java

package com.microsoft.aad.adal4jsample;

import java.io.IOException;
import java.io.UnsupportedEncodingException;
import java.net.URI;
import java.net.URLEncoder;
import java.util.Date;
import java.util.HashMap;
import java.util.Map;
import java.util.UUID;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

import javax.naming.ServiceUnavailableException;
import javax.servlet.Filter;
import javax.servlet.FilterChain;
import javax.servlet.FilterConfig;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import com.microsoft.aad.adal4j.AuthenticationContext;
import com.microsoft.aad.adal4j.AuthenticationResult;
import com.microsoft.aad.adal4j.ClientCredential;
import com.nimbusds.oauth2.sdk.AuthorizationCode;
import com.nimbusds.openid.connect.sdk.AuthenticationErrorResponse;
import com.nimbusds.openid.connect.sdk.AuthenticationResponse;
import com.nimbusds.openid.connect.sdk.AuthenticationResponseParser;
import com.nimbusds.openid.connect.sdk.AuthenticationSuccessResponse;

public class BasicFilter implements Filter {

    private String clientId = "";
    private String clientSecret = "";
    private String tenant = "";
    private String authority;

    public void destroy() {

    }

    public void doFilter(ServletRequest request, ServletResponse response,
            FilterChain chain) throws IOException, ServletException {

        if (request instanceof HttpServletRequest) {
            HttpServletRequest httpRequest = (HttpServletRequest) request;
            HttpServletResponse httpResponse = (HttpServletResponse) response;
            try {

                String currentUri = request.getScheme()
                        + "://"
                        + request.getServerName()
                        + ("http".equals(request.getScheme())
                                && request.getServerPort() == 80
                                || "https".equals(request.getScheme())
                                && request.getServerPort() == 443 ? "" : ":"
                                + request.getServerPort())
                        + httpRequest.getRequestURI();
                String fullUrl = currentUri
                        + (httpRequest.getQueryString() != null ? "?"
                                + httpRequest.getQueryString() : "");
                // check if user has a session
                if (!AuthHelper.isAuthenticated(httpRequest)) {
                    if (AuthHelper.containsAuthenticationData(httpRequest)) {
                        Map<String, String> params = new HashMap<String, String>();
                        for (String key : request.getParameterMap().keySet()) {
                            params.put(key,
                                    request.getParameterMap().get(key)[0]);
                        }
                        AuthenticationResponse authResponse = AuthenticationResponseParser
                                .parse(new URI(fullUrl), params);
                        if (AuthHelper.isAuthenticationSuccessful(authResponse)) {

                            AuthenticationSuccessResponse oidcResponse = (AuthenticationSuccessResponse) authResponse;
                            AuthenticationResult result = getAccessToken(
                                    oidcResponse.getAuthorizationCode(),
                                    currentUri);
                            createSessionPrincipal(httpRequest, result);
                        } else {
                            AuthenticationErrorResponse oidcResponse = (AuthenticationErrorResponse) authResponse;
                            throw new Exception(String.format(
                                    "Request for auth code failed: %s - %s",
                                    oidcResponse.getErrorObject().getCode(),
                                    oidcResponse.getErrorObject()
                                            .getDescription()));
                        }
                    } else {
                            // not authenticated
                            httpResponse.setStatus(302);
                            httpResponse
                                    .sendRedirect(getRedirectUrl(currentUri));
                            return;
                    }
                } else {
                    // if authenticated, how to check for valid session?
                    AuthenticationResult result = AuthHelper
                            .getAuthSessionObject(httpRequest);

                    if (httpRequest.getParameter("refresh") != null) {
                        result = getAccessTokenFromRefreshToken(
                                result.getRefreshToken(), currentUri);
                    } else {
                        if (httpRequest.getParameter("cc") != null) {
                            result = getAccessTokenFromClientCredentials();
                        } else {
                            if (result.getExpiresOnDate().before(new Date())) {
                                result = getAccessTokenFromRefreshToken(
                                        result.getRefreshToken(), currentUri);
                            }
                        }
                    }
                    createSessionPrincipal(httpRequest, result);
                }
            } catch (Throwable exc) {
                httpResponse.setStatus(500);
                request.setAttribute("error", exc.getMessage());
                httpResponse.sendRedirect(((HttpServletRequest) request)
                        .getContextPath() + "/error.jsp");
            }
        }
        chain.doFilter(request, response);
    }

    private AuthenticationResult getAccessTokenFromClientCredentials()
            throws Throwable {
        AuthenticationContext context = null;
        AuthenticationResult result = null;
        ExecutorService service = null;
        try {
            service = Executors.newFixedThreadPool(1);
            context = new AuthenticationContext(authority + tenant + "/", true,
                    service);
            Future<AuthenticationResult> future = context.acquireToken(
                    "https://graph.windows.net", new ClientCredential(clientId,
                            clientSecret), null);
            result = future.get();
        } catch (ExecutionException e) {
            throw e.getCause();
        } finally {
            service.shutdown();
        }

        if (result == null) {
            throw new ServiceUnavailableException(
                    "authentication result was null");
        }
        return result;
    }

    private AuthenticationResult getAccessTokenFromRefreshToken(
            String refreshToken, String currentUri) throws Throwable {
        AuthenticationContext context = null;
        AuthenticationResult result = null;
        ExecutorService service = null;
        try {
            service = Executors.newFixedThreadPool(1);
            context = new AuthenticationContext(authority + tenant + "/", true,
                    service);
            Future<AuthenticationResult> future = context
                    .acquireTokenByRefreshToken(refreshToken,
                            new ClientCredential(clientId, clientSecret), null,
                            null);
            result = future.get();
        } catch (ExecutionException e) {
            throw e.getCause();
        } finally {
            service.shutdown();
        }

        if (result == null) {
            throw new ServiceUnavailableException(
                    "authentication result was null");
        }
        return result;

    }

    private AuthenticationResult getAccessToken(
            AuthorizationCode authorizationCode, String currentUri)
            throws Throwable {
        String authCode = authorizationCode.getValue();
        ClientCredential credential = new ClientCredential(clientId,
                clientSecret);
        AuthenticationContext context = null;
        AuthenticationResult result = null;
        ExecutorService service = null;
        try {
            service = Executors.newFixedThreadPool(1);
            context = new AuthenticationContext(authority + tenant + "/", true,
                    service);
            Future<AuthenticationResult> future = context
                    .acquireTokenByAuthorizationCode(authCode, new URI(
                            currentUri), credential, null);
            result = future.get();
        } catch (ExecutionException e) {
            throw e.getCause();
        } finally {
            service.shutdown();
        }

        if (result == null) {
            throw new ServiceUnavailableException(
                    "authentication result was null");
        }
        return result;
    }

    private void createSessionPrincipal(HttpServletRequest httpRequest,
            AuthenticationResult result) throws Exception {
        httpRequest.getSession().setAttribute(
                AuthHelper.PRINCIPAL_SESSION_NAME, result);
    }

    private String getRedirectUrl(String currentUri)
            throws UnsupportedEncodingException {
        String redirectUrl = authority
                + this.tenant
                + "/oauth2/authorize?response_type=code%20id_token&scope=openid&response_mode=form_post&redirect_uri="
                + URLEncoder.encode(currentUri, "UTF-8") + "&client_id="
                + clientId + "&resource=https%3a%2f%2fgraph.windows.net"
                + "&nonce=" + UUID.randomUUID() + "&site_id=500879";
        return redirectUrl;
    }

    public void init(FilterConfig config) throws ServletException {
        clientId = config.getInitParameter("client_id");
        authority = config.getServletContext().getInitParameter("authority");
        tenant = config.getServletContext().getInitParameter("tenant");
        clientSecret = config.getInitParameter("secret_key");
    }

}
```

Ce servlet expose toutes les méthodes qui `ADAL4J` s’attend à partir de notre application de s’exécuter. Cela inclut :

- `getAccessTokenFromClientCredentials()`-Obtient accès à jeton à partir de notre code secret
- `getAccessTokenFromRefreshToken()`-Obtient accès à jeton à partir d’un jeton d’actualisation
- `getAccessToken()`-Obtient l’accès à jeton à partir d’un flux OpenID se connecter (que nous utilisons le cas)
- `createSessionPrincipal()`-Crée une entité de sécurité que nous utilisons pour l’accès à l’API de graphique
- `getRedirectUrl()`-Obtient la redirectURL à comparer avec la valeur que vous avez saisi dans le portail.

##<a name="compile-and-run-the-sample-in-tomcat"></a>Compiler et exécuter l’exemple dans Tomcat

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

