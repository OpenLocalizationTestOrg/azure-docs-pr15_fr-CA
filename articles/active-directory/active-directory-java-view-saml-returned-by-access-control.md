<properties
    pageTitle="Vue SAML retourné par le Service de contrôle d’accès (Java)"
    description="Découvrez comment afficher SAML retourné par le Service de contrôle d’accès dans les applications Java hébergées sur Azure."
    services="active-directory" 
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor="" />

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm" />

# <a name="how-to-view-saml-returned-by-the-azure-access-control-service"></a>Comment afficher SAML retourné par le Service de contrôle d’accès Azure

Ce guide va vous montrer comment afficher les sous-jacent Markup langage SAML (Security Assertion) retournée à votre application par le système d’exploitation Azure Access Control Service (ACS). Le guide s’appuie sur la rubrique [comment authentifier des utilisateurs Web avec Azure Access contrôle Service utilisant Eclipse][] , en fournissant le code qui affiche les informations de SAML. L’application terminée ressemblera à la suivante.

![Exemple de sortie SAML][saml_output]

Pour plus d’informations sur ACS, consultez la section [étapes suivantes](#next_steps) .

> [AZURE.NOTE]
> Le filtre de contrôle Azure Access Services est un aperçu de la technologie de communauté. En tant que version préliminaire du logiciel, il n'est pas officiellement pris en charge par Microsoft.

## <a name="prerequisites"></a>Conditions préalables

Pour exécuter les tâches présentées dans ce guide, terminer l’exemple comment [authentifier des utilisateurs Web avec Azure Access contrôle Service utilisant Eclipse][] et l’utiliser comme point de départ pour ce didacticiel.

## <a name="add-the-jspwriter-library-to-your-build-path-and-deployment-assembly"></a>Ajouter la bibliothèque JspWriter à votre assembly de chemin d’accès et de déploiement de build

Ajoutez la bibliothèque qui contient la classe **javax.servlet.jsp.JspWriter** à votre assembly de chemin d’accès et de déploiement de build. Si vous utilisez Tomcat, la bibliothèque est **jsp-api.jar**, qui se trouve dans le dossier de **lib** Apache.

1. Dans l’Explorateur de projet de Eclipse, droit **MyACSHelloWorld**et cliquez sur **Générer le chemin d’accès**, cliquez sur **Configurer un chemin de Build**, cliquez sur l’onglet **bibliothèques** , puis cliquez sur **Ajouter un JARs externe**.
2. Dans la boîte de dialogue **Sélection d’un JAR** , naviguez vers le fichier JAR nécessaire, sélectionnez-le et puis cliquez sur **Ouvrir**.
3. Le pour MyACSHelloWorld boîte de dialogue **Propriétés** est ouverte, cliquez sur **Ensemble de déploiement**.
4. Dans la boîte de dialogue **Assemblage de déploiement Web** , cliquez sur **Ajouter**.
5. Dans la boîte de dialogue **Nouvelle Directive d’Assembly** , cliquez sur **Entrées du chemin d’accès de Build Java** et puis cliquez sur **suivant**.
6. Sélectionnez la librairie appropriée et cliquez sur **Terminer**.
7. Cliquez sur **OK** pour fermer la boîte de dialogue **Propriétés de MyACSHelloWorld** .

## <a name="modify-the-jsp-file-to-display-saml"></a>Modifiez le fichier JSP pour afficher SAML

Modifiez **index.jsp** pour utiliser le code suivant.

    <%@ page language="java" contentType="text/html; charset=UTF-8"
        pageEncoding="UTF-8"%>
        <%@ page import="javax.xml.parsers.*"
                 import="javax.xml.transform.*"
                 import="org.w3c.dom.*"
                 import="java.io.*"
                 import="javax.xml.transform.stream.*"
                 import="javax.xml.transform.dom.*"
                 import="javax.xml.xpath.*"
                 import="javax.servlet.jsp.JspWriter" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Sample ACS Filter</title>
    </head>
    <body>
        <h3>SAML information from sample ACS program</h3>
        <%!
        void displaySAMLInfo(Node node, String parent, JspWriter out)
        {
        
            try
            {
                String nodeName;
                int nChild, i;
                
                nodeName = node.getNodeName();
                out.println("<br>");
                out.println("<u>Examining <b>" + parent + nodeName + "</b></u><br>");
                   
                   // Attributes.
                   NamedNodeMap attribsMap = node.getAttributes();
                   if (null != attribsMap)
                   {
                         for (i=0; i < attribsMap.getLength(); i++)
                         {
                                Node attrib = attribsMap.item(i);
                                out.println("Attribute: <b>" + attrib.getNodeName() + "</b>: " + attrib.getNodeValue()  + "<br>");
                         }
                   }
                   
                   // Child nodes.
                   NodeList list = node.getChildNodes();
                   if (null != list)
                   {
                          nChild = list.getLength();
                          if (nChild > 0)
                          {                    
    
                                 // If it is a text node, just print the text.
                                 if (list.item(0).getNodeName() == "#text")
                                 {
                                     out.println("Text value: <b>" + list.item(0).getTextContent() + "</b><br>");
                                 }
                                 else
                                 {
                                     // Print out the child node names.
                                     out.print("Contains " + nChild + " child node(s): ");   
                                     for (i=0; i < nChild; i++)
                                     {
                                        Node temp = list.item(i);
                                        
                                        out.print("<b>" + temp.getNodeName() + "</b>");
                                        if (i < nChild - 1)
                                        {
                                            // Separate the names.
                                            out.print(", ");
                                        }
                                        else
                                        {
                                            // Finish the sentence.
                                            out.print(".");
                                        }
                                            
                                     }
                                     out.println("<br>");
                                     
                                     // Process the child nodes.
                                     for (i=0; i < nChild; i++)
                                     {
                                        Node temp = list.item(i);
                                        displaySAMLInfo(temp, parent + nodeName + "\\", out);
                                     }
                               }
                          }
                      }
                  }
                catch (Exception e)
                {
                    System.out.println("Exception encountered.");
                    e.printStackTrace();            
                }
            }
        %>
    
        <%
        try 
        {
            String data  = (String) request.getAttribute("ACSSAML");
            
            DocumentBuilder docBuilder;
            Document doc = null;
            DocumentBuilderFactory docBuilderFactory = DocumentBuilderFactory.newInstance();
            docBuilderFactory.setIgnoringElementContentWhitespace(true);
            docBuilder = docBuilderFactory.newDocumentBuilder();
            byte[] xmlDATA = data.getBytes();
            
            ByteArrayInputStream in = new ByteArrayInputStream(xmlDATA); 
            doc = docBuilder.parse(in);
            doc.getDocumentElement().normalize();
            
            // Iterate the child nodes of the doc.
            NodeList list = doc.getChildNodes();
    
            for (int i=0; i < list.getLength(); i++)
            {
                displaySAMLInfo(list.item(i), "", out);
            }
        }
        catch (Exception e) 
        {
            out.println("Exception encountered.");
            e.printStackTrace();
        }
        
        %>
    </body>
    </html>

## <a name="run-the-application"></a>Exécution de l’application

1. Exécutez votre application dans l’émulateur de l’ordinateur ou déployer sur Azure, à l’aide de la procédure décrite comment [authentifier des utilisateurs Web avec Azure Access contrôle Service à l’aide d’Eclipse][].
2. Lancez un navigateur et ouvrez votre application web. Une fois que vous ouvrez une session sur votre application, vous verrez des informations SAML, y compris l’assertion de sécurité fournie par le fournisseur d’identité.

## <a name="next-steps"></a>Étapes suivantes

Autres Explorer les fonctionnalités d’ACS et pour tester des scénarios plus sophistiqués, voir [Service de contrôle de Access 2.0][].

[Prerequisites]: #pre
[Modify the JSP file to display SAML]: #modify_jsp
[Add the JspWriter library to your build path and deployment assembly]: #add_library
[Run the application]: #run_application
[Next steps]: #next_steps
[Service de contrôle d’accès 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[Comment faire pour authentifier les utilisateurs Web avec le Service de contrôle d’accès de Azure utilisant Eclipse]: ../active-directory-java-authenticate-users-access-control-eclipse
[saml_output]: ./media/active-directory-java-view-saml-returned-by-access-control/SAML_Output.png
 