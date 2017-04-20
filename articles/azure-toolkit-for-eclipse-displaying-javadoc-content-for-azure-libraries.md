<properties
    pageTitle="Affichage du contenu de Javadoc dans Eclipse pour le Package de bibliothèques Azure pour Java"
    description="Comment afficher le contenu de Javadoc pour les bibliothèques d’Azure dans Eclipse."
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

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh698319.aspx -->

# <a name="displaying-javadoc-content-in-eclipse-for-the-azure-libraries-package-for-java"></a>Affichage du contenu de Javadoc dans Eclipse pour le Package de bibliothèques Azure pour Java #

Le contenu de Javadoc pour les bibliothèques d’Azure pour Java peut être visualisé au sein de votre environnement d’Eclipse en associant le contenu Javadoc aux bibliothèques Azure pour Java. Les étapes suivantes vous montrent comment utiliser cette fonctionnalité au sein d’Eclipse.

Cette procédure suppose que vous avez déjà ajouté à la bibliothèque d’Azure pour Java à votre chemin d’accès de build.

## <a name="to-display-javadoc-content-in-eclipse-for-the-azure-libraries-for-java"></a>Pour afficher le contenu de Javadoc dans Eclipse pour les bibliothèques d’Azure pour Java ##

* Dans l’Explorateur de projet de Eclipse, dans la section **Bibliothèques de référence** de votre projet, ouvrez le menu contextuel pour la bibliothèque Azure pour Java JAR. Par exemple, **microsoft-windowsazure-api-0.1.0.jar** (le numéro de version peut être différent, dépendant de la version que vous avez installé).
* Cliquez sur **Propriétés**.
* Dans la boîte de dialogue **Propriétés** , dans le volet gauche, cliquez sur **L’emplacement Javadoc**. La boîte de dialogue **Javadoc emplacement** s’affiche.
* Vous pouvez spécifier une **URL de Javadoc**ou un **Javadoc dans l’archive**.
    * Si vous choisissez de spécifier une **URL de Javadoc**, utilisez les URL comme **http://dl.windowsazure.com/javadoc** ou **http://dl.windowsazure.com/storage/javadoc**.
    * Si vous choisissez d’utiliser **Javadoc dans l’archive**, vous pouvez spécifier un fichier externe, ou un fichier d’espace de travail.
    Faites votre choix et parcourir/valider en fonction des besoins. L’exemple suivant associe les bibliothèques Azure pour Java avec le JAR de Javadoc correspondante qui a été téléchargée localement dans un dossier nommé **c:\MyAzureJARs**.
    ![][ic553487]
* *Facultatif*: cliquez sur **Valider**. Les problèmes potentiels avec le JAR Javadoc pourraient être affichées ici.
* Cliquez sur **OK**.

Une fois associé à la bibliothèque, le contenu Javadoc doit afficher votre IDE Eclipse. Par exemple, si `blob` est défini de type `CloudBlockBlob` dans votre code, les éléments suivants sont un exemple de contenu Javadoc qui s’affiche lorsque vous tapez `blob.acquireLease` dans le code :

![][ic553488]

## <a name="see-also"></a>Voir aussi ##

[Azure Shared Computer Toolkit pour Éclipse][]

[Création d’une Application du monde Hello pour Azure dans Éclipse][]

[L’installation de la Shared Computer Toolkit Azure pour Éclipse][] 

Pour plus d’informations sur l’utilisation d’Azure avec Java, consultez le [Centre pour développeurs Java Azure][].

<!-- URL List -->

[Centre de développement Java Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Shared Computer Toolkit pour Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Création d’une Application du monde Hello pour Azure dans Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[L’installation de la Shared Computer Toolkit Azure pour Éclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic553487]: ./media/azure-toolkit-for-eclipse-displaying-javadoc-content-for-azure-libraries/ic553487.png
[ic553488]: ./media/azure-toolkit-for-eclipse-displaying-javadoc-content-for-azure-libraries/ic553488.png