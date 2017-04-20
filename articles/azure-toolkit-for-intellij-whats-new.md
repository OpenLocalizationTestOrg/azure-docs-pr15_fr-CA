<properties
    pageTitle="Nouveautés de la Shared Computer Toolkit Azure pour IntelliJ | Microsoft Azure"
    description="Découvrez les dernières fonctionnalités de la Shared Computer Toolkit Azure pour IntelliJ."
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
    ms.date="08/26/2016" 
    ms.author="robmcm;asirveda;martinsawicki"/>

# <a name="whats-new-in-the-azure-toolkit-for-intellij"></a>Nouveautés de la Shared Computer Toolkit Azure pour IntelliJ

## <a name="azure-toolkit-for-intellij-releases"></a>Azure Shared Computer Toolkit pour les versions de IntelliJ

Cet article contient des informations sur les différentes versions et les dernières mises à jour pour le Shared Computer Toolkit Azure pour IntelliJ.

> [AZURE.NOTE] Il existe également un Shared Computer Toolkit Azure pour l’IDE Eclipse. Pour plus d’informations, reportez-vous à la section [Shared Computer Toolkit Azure pour Eclipse].

### <a name="august-26-2016"></a>26 août 2016

Le Shared Computer Toolkit Azure pour IntelliJ - août 2016 version inclut les améliorations suivantes :

* **Les Distributions de JDK personnalisé**. Le Shared Computer Toolkit Azure pour IntelliJ prend désormais en charge la spécification et le déploiement d’une version JDK arbitraire pour votre conteneur de WebApp d’Azure :
  - Outre les kits JDK fourni par Azure, vous pouvez choisir à partir d’un large choix de versions Zoulou OpenJDK mis à disposition sur Azure par des systèmes de Azul.
  - Vous pouvez également spécifier votre propre distribution JDK si vous téléchargez un fichier ZIP sur votre compte de stockage.
* **Améliorations apportées à la vue Explorateur d’Azure**:
  - Prise en charge de la gestion des machines virtuelles à l’aide du nouveau modèle de gestionnaire de ressources du Azure : vous pouvez répertorier, créer et supprimer des machines virtuelles resource manager sans quitter l’IDE.
  - Prise en charge pour la gestion des comptes de stockage blob à l’aide du Gestionnaire de ressources d’Azure, qui complète les fonctionnalités de gestion des comptes de stockage « classique ».
* **Le pilote JDBC de Microsoft 6.0 pour SQL Server**. Cette mise à jour inclut le dernier pilote JDBC de Microsoft SQL Server (version 6.0), qui est maintenant inclus dans une bibliothèque que vous pouvez facilement ajouter à vos projets Java, ainsi remplacer l’ancienne version.

### <a name="june-29-2016"></a>29 juin 2016

Le Shared Computer Toolkit Azure pour IntelliJ - juin 2016 version inclut les améliorations suivantes :

* **Exigence de Java 8**. Le Shared Computer Toolkit Azure pour IntelliJ requiert désormais Java 8, bien que cette exigence n'est que pour la boîte à outils - vos applications peuvent continuer à utiliser toutes les versions de Java sont prises en charge par Azure.
* **Prise en charge pour les kits de JDK Java plus récente**. Les dernières versions des kits JDK Java sont désormais pris en charge par le Shared Computer Toolkit Azure pour IntelliJ.
* **Prise en charge pour Azure SDK v2.9.1**. La dernière version du SDK Azure est maintenant la minimum requise pour le Shared Computer Toolkit Azure pour IntelliJ.
* **Exemples intégrés**. Le Shared Computer Toolkit Azure pour IntelliJ propose maintenant plusieurs exemples d’applications pour aider les développeurs à commencer.
* **Intégration d’outils de HDInsight**. Outils d’HDInsight d’Azure sont désormais fournis avec le Shared Computer Toolkit Azure pour IntelliJ. Pour plus d’informations, consultez [HDInsight plug-in d’outils pour IntelliJ].
* **Débogage d’applications Web de Java à distance**. Le Shared Computer Toolkit Azure pour IntelliJ prend désormais en charge le débogage distant des applications web de Java sur le Service d’application Azure.

### <a name="april-12-2016"></a>12 avril 2016

Le Shared Computer Toolkit Azure pour IntelliJ - avril 2016 version inclut les améliorations suivantes :

* **Prise en charge pour Azure SDK v2.9.0**. La dernière version du SDK Azure est maintenant la minimum requise pour le Shared Computer Toolkit Azure pour IntelliJ.
* **Utilisation de divers, amélioration de la réactivité et les performances liées à la prise en charge de l’application Web de Azure**. Un certain nombre d’optimisations des performances dans comment le Shared Computer Toolkit communique avec résultat Azure dans une interface utilisateur plus réactive.
* **Possibilité de supprimer un conteneur d’Application Web existant dans Azure à partir de IntelliJ**. Le Shared Computer Toolkit Azure pour IntelliJ maintenant vous permet de supprimer un conteneur Azure Web existant sans laisser de IntelliJ.

## <a name="see-also"></a>Voir aussi ##

Pour plus d’informations sur les boîtes à outils Azure pour IDE de Java, consultez les liens suivants :

- [Azure Shared Computer Toolkit pour Éclipse]
  - [L’installation de la Shared Computer Toolkit Azure pour Éclipse]
  - [Créer une application de Web de Hello World pour Azure dans Éclipse]
  - [Quelles sont les nouveautés dans la Azure Shared Computer Toolkit pour Éclipse]
- [Azure Shared Computer Toolkit pour IntelliJ]
  - [L’installation de la Shared Computer Toolkit Azure pour IntelliJ]
  - [Créer une application de Web de Hello World pour Azure dans IntelliJ]
  - *Nouveautés de la Shared Computer Toolkit Azure pour IntelliJ (Cet Article)*

Pour plus d’informations sur l’utilisation d’Azure avec Java, consultez le [Centre pour développeurs Java Azure].

<!-- URL List -->

[Azure Shared Computer Toolkit pour Éclipse]: ./azure-toolkit-for-eclipse.md
[Azure Shared Computer Toolkit pour IntelliJ]: ./azure-toolkit-for-intellij.md
[Créer une application de Web de Hello World pour Azure dans Éclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Créer une application de Web de Hello World pour Azure dans IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[L’installation de la Shared Computer Toolkit Azure pour Éclipse]: ./azure-toolkit-for-eclipse-installation.md
[L’installation de la Shared Computer Toolkit Azure pour IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Quelles sont les nouveautés dans la Azure Shared Computer Toolkit pour Éclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[What's New in the Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Centre de développement Java Azure]: http://go.microsoft.com/fwlink/?LinkID=699547

[Plug-in d’outils HDInsight pour IntelliJ]: ./hdinsight/hdinsight-apache-spark-intellij-tool-plugin.md
