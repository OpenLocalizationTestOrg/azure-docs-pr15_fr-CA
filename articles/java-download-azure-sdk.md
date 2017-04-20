<properties 
    pageTitle="Télécharger le SDK Azure pour Java" 
    description="Découvrez comment télécharger Azure SDK pour Java, avec l’exemple de code fourni pour les projets Maven et les étapes de l’installation de base pour le Kit de ressources d’Azure pour Eclipse." 
    services="" 
    documentationCenter="java" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="multiple" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="download-the-azure-sdk-for-java"></a>Télécharger le SDK Azure pour Java

Cet article contient des instructions pour télécharger et installer les bibliothèques Azure pour Java.

**Remarque :** Les bibliothèques d’Azure pour Java sont distribuées sous la [licence Apache, Version 2.0][license].

## <a name="azure-libraries-for-java---manual-download"></a>Bibliothèques Azure pour Java - téléchargement manuel

Pour installer manuellement les bibliothèques de Azure pour Java, cliquez sur <http://go.microsoft.com/fwlink/?LinkId=690320> pour télécharger un fichier ZIP qui contient toutes les bibliothèques et toutes les dépendances.

Une fois que vous avez téléchargé le fichier zip sur votre ordinateur, extraire le contenu et utilisez une des options suivantes pour ajouter les fichiers JAR à votre projet :

* Importer les fichiers JAR dans votre projet Java dans Eclipse.

* Configurer le **Chemin d’accès de Build** pour votre projet Java dans Eclipse pour inclure le chemin d’accès vers les fichiers JAR.

Pour plus d’informations sur la configuration des chemins d’accès de build dans Eclipse, consultez l’article de [Chemin d’accès de la génération de Java] sur le site Web d’Eclipse.

**Remarque :** Consultez le license.txt et le fichier ThirdPartyNotices.txt à l’intérieur du ZIP pour la licence et d’autres informations.

## <a name="azure-libraries-for-java---building-with-maven"></a>Bibliothèques Azure pour Java - génération avec Maven

### <a name="step-1---set-up-your-project-to-use-maven-for-build"></a>Étape 1 : configurer votre projet pour utiliser Maven pour génération

Pour créer des projets de Maven dans Eclipse qui utilisent les bibliothèques Azure pour Java, en suivant les instructions fournies dans le [Guide de mise en route avec les bibliothèques de gestion Azure pour Java] [ maven-getting-started] l’article. 

### <a name="step-2---configure-your-maven-settings-with-the-requisite-dependencies"></a>Étape 2 : configurer vos paramètres Maven avec les dépendances requises

Une fois que votre projet a été configuré pour utiliser Maven de build, vous pouvez ajouter les dépendances nécessaires à votre fichier pom.xml à l’aide de la syntaxe comme l’exemple suivant. Notez que vous n’avez pas besoin d’ajouter chaque dépendance qui est répertorié dans l’exemple suivant ; Vous devrez ajouter les dépendances spécifiques qui nécessite de votre projet.

> [AZURE.NOTE] Au sein de chaque `<version>` élément dans l’exemple suivant, remplacez les espaces réservés de « n.n.n » dans cet exemple les numéros de version valides, qui peuvent être obtenus à partir du [Référentiel de bibliothèques Azure sur Maven].

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-compute</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-network</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-sql</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-storage</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-websites</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-media</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-serviceruntime</artifactId>
        <version>n.n.n</version>
    </dependency>

## <a name="installing-the-azure-toolkit-for-eclipse"></a>L’installation de la Shared Computer Toolkit Azure pour Éclipse

Cette section contient des instructions de base pour l’installation de la Shared Computer Toolkit Azure pour Eclipse ; Pour obtenir des instructions détaillées, voir [installation le Shared Computer Toolkit Azure pour Eclipse].

### <a name="prerequisites"></a>Conditions préalables

1. Systèmes d’exploitation Windows répertoriés dans l’article [What's New dans le Shared Computer Toolkit Azure pour Eclipse] .
1. Systèmes d’exploitation des Macintosh ou Linux répertoriées dans l’article [What's New dans le Shared Computer Toolkit Azure pour Eclipse] .
1. Eclipse IDE pour les développeurs Java EE, Indigo ou une version ultérieure. Cela peut être téléchargé à partir de <http://www.eclipse.org/downloads/>.

### <a name="basic-installation-steps"></a>Étapes de l’Installation

1. Dans Eclipse, dans le menu **aide** , sélectionnez **Installer un nouveau logiciel**.
1. Entrez l' emplacement de site <http://dl.microsoft.com/eclipse> , puis appuyez sur **entrée**.
1. Sélectionnez les éléments à installer et cliquez sur **Terminer**.

Le Shared Computer Toolkit Azure pour Eclipse utilise la dernière version du SDK Azure. Cela peut être téléchargé à l’aide de Web Platform Installer (WebPI) à <http://go.microsoft.com/fwlink/?LinkID=252838>. Toutefois, si vous ne l’avez pas installée, lorsque vous créez votre premier projet de déploiement d’Azure, le Shared Computer Toolkit Azure pour Eclipse installe automatiquement la version appropriée du SDK Azure.

## <a name="see-also"></a>Voir aussi

[Azure Shared Computer Toolkit pour Éclipse]

[L’installation de la Shared Computer Toolkit Azure pour Éclipse] 

[Création d’une Application du monde Hello pour Azure dans Éclipse]

Pour plus d’informations sur l’utilisation d’Azure avec Java, consultez le [Centre pour développeurs Java Azure].

<!-- URL List -->

[Centre de développement Java Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Référentiel de bibliothèques Azure sur Maven]: http://go.microsoft.com/fwlink/?LinkID=286274
[Azure Shared Computer Toolkit pour Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Création d’une Application du monde Hello pour Azure dans Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[L’installation de la Shared Computer Toolkit Azure pour Éclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Chemin d’accès de la version Java]: http://help.eclipse.org/luna/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2Freference%2Fref-properties-build-path.htm
[license]: http://www.apache.org/licenses/LICENSE-2.0.html
[maven-getting-started]: http://go.microsoft.com/fwlink/?LinkID=622998
[zip-download]: http://go.microsoft.com/fwlink/?LinkId=690320
[Quelles sont les nouveautés dans la Azure Shared Computer Toolkit pour Éclipse]: http://go.microsoft.com/fwlink/?LinkId=690333
