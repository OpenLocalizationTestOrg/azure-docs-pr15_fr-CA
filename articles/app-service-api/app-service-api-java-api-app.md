<properties
    pageTitle="Générer et déployer une application Java API dans le Service d’application Azure"
    description="Apprenez à créer un package d’application Java API et la déployer sur les services d’application Azure."
    services="app-service\api"
    documentationCenter="java"
    authors="bradygaster"
    manager="mohisri"
    editor="tdykstra"/>

<tags
    ms.service="app-service-api"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="java"
    ms.topic="get-started-article"
    ms.date="08/31/2016"
    ms.author="rachelap"/>

# <a name="build-and-deploy-a-java-api-app-in-azure-app-service"></a>Générer et déployer une application Java API dans le Service d’application Azure

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

Ce didacticiel explique comment créer une application Java et le déployer vers Azure App Apps de l’API de Service à l’aide de [Git]. Vous pouvez suivre les instructions de ce didacticiel sur n’importe quel système d’exploitation qui est capable d’exécuter Java. Le code de ce didacticiel est généré à l’aide de [Maven]. [JAX-r] est utilisé pour créer le Service RESTful et est généré en fonction de la spécification de métadonnées [Swagger] à l’aide de l' [Éditeur de Swagger].

## <a name="prerequisites"></a>Conditions préalables

1. [Kit du développeur Java 8] \(ou version ultérieure)
1. [Maven] installé sur votre ordinateur de développement
1. [Git] installé sur votre ordinateur de développement
1. Un abonnement payé ou [version d’évaluation gratuite] de [Microsoft Azure]
1. Une application de test HTTP comme [Postman]

## <a name="scaffold-the-api-using-swaggerio"></a>Scaffold l’API à l’aide de Swagger.IO

À l’aide de l’éditeur en ligne swagger.io, vous pouvez saisir le code Swagger de JSON ou YAML qui représente la structure de votre API. Une fois que la surface d’API conçue, vous pouvez exporter pour une variété de plates-formes et infrastructures. Dans la section suivante, le code avec génération de modèles sera modifié pour inclure les fonctionnalités de simulation. 

Cette démonstration commence avec un corps de Swagger de JSON qui vous allez coller dans l’éditeur de swagger.io, qui sera ensuite utilisé pour générer le code, ce qui permet d’accéder à un point de terminaison API REST de JAX-r. Ensuite, vous allez modifier le code avec génération de modèles pour retourner des données fictives, simulant une API REST basées sur un mécanisme de persistance des données.  

1. Copiez le code suivant de la Swagger de JSON à votre Presse-papiers :

        {
            "swagger": "2.0",
            "info": {
                "version": "v1",
                "title": "Contact List",
                "description": "A Contact list API based on Swagger and built using Java"
            },
            "host": "localhost",
            "schemes": [
                "http",
                "https"
            ],
            "basePath": "/api",
            "paths": {
                "/contacts": {
                    "get": {
                        "tags": [
                            "Contact"
                        ],
                        "operationId": "contacts_get",
                        "consumes": [],
                        "produces": [
                            "application/json",
                            "text/json"
                        ],
                        "responses": {
                            "200": {
                                "description": "OK",
                                "schema": {
                                    "type": "array",
                                    "items": {
                                        "$ref": "#/definitions/Contact"
                                    }
                                }
                            }
                        },
                        "deprecated": false
                    }
                },
                "/contacts/{id}": {
                    "get": {
                        "tags": [
                            "Contact"
                        ],
                        "operationId": "contacts_getById",
                        "consumes": [],
                        "produces": [
                            "application/json",
                            "text/json"
                        ],
                        "parameters": [
                            {
                                "name": "id",
                                "in": "path",
                                "required": true,
                                "type": "integer",
                                "format": "int32"
                            }
                        ],
                        "responses": {
                            "200": {
                                "description": "OK",
                                "schema": {
                                    "type": "array",
                                    "items": {
                                        "$ref": "#/definitions/Contact"
                                    }
                                }
                            }
                        },
                        "deprecated": false
                    }
                }
            },
            "definitions": {
                "Contact": {
                    "type": "object",
                    "properties": {
                        "Id": {
                            "format": "int32",
                            "type": "integer"
                        },
                        "Name": {
                            "type": "string"
                        },
                        "EmailAddress": {
                            "type": "string"
                        }
                    }
                }
            }
        }

1. Accédez à la [Swagger en ligne de l’éditeur]. Une seule fois, cliquez sur l’élément de menu **fichier -> Coller JSON** .

    ![Élément de menu Coller JSON][paste-json]

1. Collez les Contacts liste API Swagger JSON copiés précédemment. 

    ![Collage de code JSON dans Swagger][pasted-swagger]

1. Permet d’afficher les pages de la documentation et le résumé de API restitué dans l’éditeur. 

    ![Vue Swagger généré Docs][view-swagger-generated-docs]

1. Sélectionnez l’option de menu **Générer le serveur-> JAX-RS** pour scaffold le code côté serveur que vous allez modifier ultérieurement pour ajouter une implémentation factice. 

    ![Générer un élément de Menu de Code][generate-code-menu-item]

    Une fois que le code est généré, vous recevrez un fichier ZIP de téléchargement. Ce fichier contient le code avec génération de modèles par le Générateur de code Swagger, et toutes à créer des scripts. Décompressez la bibliothèque entière dans un répertoire sur votre station de travail de développement. 

## <a name="edit-the-code-to-add-api-implementation"></a>Modifier le Code pour ajouter la mise en oeuvre de l’API

Dans cette section, vous allez remplacer mise en oeuvre du côté serveur du code généré par Swagger avec votre code personnalisé. Le nouveau code retournera un entités ArrayList de Contact au client appelant. 

1. Ouvrez le fichier de modèle *Contact.java* , qui se trouve dans le dossier de *génération/src/java/swagger/io/modèle* , à l’aide de votre éditeur de texte ou de [Code de Visual Studio] . 

    ![Fichier de modèle de Contact ouvert][open-contact-model-file]

1. Ajoutez le constructeur suivant à la classe **Contact** . 

        public Contact(Integer id, String name, String email) 
        {
            this.id = id;
            this.name = name;
            this.emailAddress = email;
        }

1. Ouvrez le fichier d’implémentation de service *ContactsApiServiceImpl.java* , qui se trouve dans le dossier *src/main/java/e/s/swagger/api/impl* , à l’aide de votre éditeur de texte ou de [Code de Visual Studio] .

    ![Fichier de Code de Service Contact en cours][open-contact-service-code-file]

1. Remplacer le code dans le fichier avec ce nouveau code pour ajouter une implémentation factice pour le code de service. 

        package io.swagger.api.impl;

        import io.swagger.api.*;
        import io.swagger.model.*;
        import com.sun.jersey.multipart.FormDataParam;
        import io.swagger.model.Contact;
        import java.util.*;
        import io.swagger.api.NotFoundException;
        import java.io.InputStream;
        import com.sun.jersey.core.header.FormDataContentDisposition;
        import com.sun.jersey.multipart.FormDataParam;
        import javax.ws.rs.core.Response;
        import javax.ws.rs.core.SecurityContext;

        @javax.annotation.Generated(value = "class io.swagger.codegen.languages.JaxRSServerCodegen", date = "2015-11-24T21:54:11.648Z")
        public class ContactsApiServiceImpl extends ContactsApiService {
  
            private ArrayList<Contact> loadContacts()
            {
                ArrayList<Contact> list = new ArrayList<Contact>();
                list.add(new Contact(1, "Barney Poland", "barney@contoso.com"));
                list.add(new Contact(2, "Lacy Barrera", "lacy@contoso.com"));
                list.add(new Contact(3, "Lora Riggs", "lora@contoso.com"));
                return list;
            }
  
            @Override
            public Response contactsGet(SecurityContext securityContext)
            throws NotFoundException {
                ArrayList<Contact> list = loadContacts();
                return Response.ok().entity(list).build();
                }
  
            @Override
            public Response contactsGetById(Integer id, SecurityContext securityContext)
            throws NotFoundException {
                ArrayList<Contact> list = loadContacts();
                Contact ret = null;
            
                for(int i=0; i<list.size(); i++)
                {
                    if(list.get(i).getId() == id)
                        {
                            ret = list.get(i);
                        }
                }
                return Response.ok().entity(ret).build();
            }
        }

1. Ouvrez une invite de commandes et placez-vous dans le dossier racine de votre application.

1. Exécutez la commande Maven suivante pour générer le code et l’exécuter à l’aide de serveur d’applications de la jetée que localement. 

        mvn package jetty:run

1. Vous devez voir la fenêtre de commande refléter que jetée a démarré votre code sur le port 8080. 

    ![Fichier de Code de Service Contact en cours][run-jetty-war]

1. Utilisez [Postman] pour effectuer une requête sur « obtenir tous les contacts » méthode de l’API à http://localhost : 8080/api/contacts.

    ![Appeler les API de Contacts][calling-contacts-api]

1. [Postman] permet de faire appel à la méthode API « get contact spécifique » est située à http://localhost : 8080/api/contacts/2.

    ![Appeler les API de Contacts][calling-specific-contact-api]

1. Pour finir, créez le fichier WAR de Java (ARchive Web) en exécutant la commande Maven suivante dans la console. 

        mvn package war:war

1. Une fois le fichier WAR est créé, il sera placé dans le dossier **cible** . Naviguer dans le dossier **cible** et renommez le fichier WAR **ROOT.war**. (Assurez-vous que la casse correspond à ce format).

         rename swagger-jaxrs-server-1.0.0.war ROOT.war

1. Enfin, exécutez les commandes suivantes à partir du dossier racine de votre application pour créer un dossier de **déploiement** à utiliser pour déployer le fichier WAR sur Azure. 

         mkdir deploy
         mkdir deploy\webapps
         copy target\ROOT.war deploy\webapps
         cd deploy

## <a name="publish-the-output-to-azure-app-service"></a>Publier la sortie au Service d’application Azure

Dans cette section, vous allez apprendre à créer une nouvelle application API via le portail d’Azure, préparer cette application API pour héberger les applications Java et déployer le fichier WAR nouvellement créée au Service d’application Azure pour exécuter votre nouvelle application d’API. 

1. Créez une nouvelle application API dans [Azure portal], en cliquant sur l’élément de menu **Nouveau -> Web + Mobile -> API app** , entrez les détails de votre application, puis en cliquant sur **créer**.

    ![Créer une nouvelle application API][create-api-app]

1. Une fois que votre application API a été créée, ouvrir la lame des **paramètres** de votre application, puis cliquez sur l’élément de menu **paramètres de l’Application** . Sélectionnez les dernières versions de Java à partir des options disponibles, puis la dernière Tomcat dans le menu **Web conteneur** et puis cliquez sur **Enregistrer**.

    ![Définir dans la lame API application Java][set-up-java]

1. Cliquez sur l’élément de menu paramètres **les informations d’identification de déploiement** et fournir un nom d’utilisateur et le mot de passe à utiliser pour la publication de fichiers à votre application d’API. 

    ![Informations d’identification du déploiement de jeu][deployment-credentials]

1. Cliquez sur l’élément de menu paramètres **source du déploiement** . Une seule fois, cliquez sur le bouton **Choisir source** , sélectionnez l’option **Local Git référentiel** et puis cliquez sur **OK**. Cette opération crée un référentiel Git exécute dans Azure, qui a une association avec votre application d’API. Chaque fois que vous validez un code à la branche *principale* de votre référentiel Git, votre code sera publié dans votre instance API application en temps réel en cours d’exécution. 

    ![Configurer un nouveau référentiel Git local][select-git-repo]

1. Copier URL du référentiel Git nouvelle dans le Presse-papiers. Enregistrer ce paramètre car il sera important dans un moment. 

    ![Configurer un nouveau référentiel Git pour votre application][copy-git-repo-url]

1. GIT pousser le fichier WAR dans le référentiel en ligne. Pour ce faire, naviguez dans le dossier **deploy** que vous avez créés précédemment afin que peut valider aisément le code dans le référentiel en cours d’exécution dans votre application de Service. Une fois que vous êtes dans la fenêtre de console et navigué dans le dossier où se trouve le dossier d’applications Web, exécutez les commandes suivantes de la Git pour lancer le processus et de lancer un déploiement. 

         git init
         git add .
         git commit -m "initial commit"
         git remote add azure [YOUR GIT URL]
         git push azure master

    Une fois que vous exécutez la requête **push** , on vous demandera le mot de passe que vous avez créé précédemment pour les informations d’identification de déploiement. Après avoir entré vos informations d’identification, vous devez voir votre portail afficher que la mise à jour a été déployée.

1. Si vous utilisez une fois de plus Postman atteint le nouvellement déployé API application qui s’exécute dans le Service d’application Azure, vous verrez que le comportement est cohérent et que désormais il retourne les données de contact comme prévu, et à l’aide de modifications du code simple pour le Swagger.io avec génération de modèles code de Java. 

    ![À l’aide de votre API REST de Contacts Java live dans Azure][postman-calling-azure-contacts]

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez réussi à démarrer avec un fichier de Swagger de JSON et certaines avec génération de modèles code Java obtenu à partir de l’éditeur Swagger.io. À partir de là, les modifications simples et un Git déploiement des processus ayant une application API fonctionnelle écrite en Java a généré. L’affiche de didacticiel suivant comment [utiliser les API des applications à partir de clients de JavaScript, à l’aide de CORS][App Service API CORS]. Dans la série de didacticiels suivants montrent comment implémenter l’authentification et l’autorisation.

Pour créer dans cet exemple, vous pouvez plus d’informations sur le [Stockage des SDK pour Java] pour rendre persistantes les blobs JSON. Ou bien, le [Document base de données Java SDK] vous permet d’enregistrer vos données de Contact pour la base de données de Document Azure. 

Pour plus d’informations sur l’utilisation de Java dans Azure, consultez le [Centre pour développeurs Java].

<!-- URL List -->

[App Service API CORS]: app-service-api-cors-consume-javascript.md
[Azure portal]: https://portal.azure.com/
[Document de base de données Java SDK]: ../documentdb/documentdb-java-application.md
[version d’évaluation gratuite]: https://azure.microsoft.com/pricing/free-trial/
[GIT]: http://www.git-scm.com/
[Centre pour développeurs Java]: /develop/java/
[Kit du développeur Java 8]: http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html
[JAX-RS]: https://jax-rs-spec.java.net/
[Maven]: https://maven.apache.org/
[Microsoft Azure]: https://azure.microsoft.com/
[Éditeur de Swagger en ligne]: http://editor.swagger.io/
[Postman]: https://www.getpostman.com/
[Stockage SDK pour Java]: ../storage/storage-java-how-to-use-blob-storage.md
[Swagger]: http://swagger.io/
[Éditeur de swagger]: http://editor.swagger.io/
[Code de Visual Studio]: https://code.visualstudio.com

<!-- IMG List -->

[paste-json]: ./media/app-service-api-java-api-app/paste-json.png
[pasted-swagger]: ./media/app-service-api-java-api-app/pasted-swagger.png
[view-swagger-generated-docs]: ./media/app-service-api-java-api-app/view-swagger-generated-docs.png
[generate-code-menu-item]: ./media/app-service-api-java-api-app/generate-code-menu-item.png
[open-contact-model-file]: ./media/app-service-api-java-api-app/open-contact-model-file.png
[open-contact-service-code-file]: ./media/app-service-api-java-api-app/open-contact-service-code-file.png
[run-jetty-war]: ./media/app-service-api-java-api-app/run-jetty-war.png
[calling-contacts-api]: ./media/app-service-api-java-api-app/calling-contacts-api.png
[calling-specific-contact-api]: ./media/app-service-api-java-api-app/calling-specific-contact-api.png
[create-api-app]: ./media/app-service-api-java-api-app/create-api-app.png
[set-up-java]: ./media/app-service-api-java-api-app/set-up-java.png
[deployment-credentials]: ./media/app-service-api-java-api-app/deployment-credentials.png
[select-git-repo]: ./media/app-service-api-java-api-app/select-git-repo.png
[copy-git-repo-url]: ./media/app-service-api-java-api-app/copy-git-repo-url.png
[postman-calling-azure-contacts]: ./media/app-service-api-java-api-app/postman-calling-azure-contacts.png
