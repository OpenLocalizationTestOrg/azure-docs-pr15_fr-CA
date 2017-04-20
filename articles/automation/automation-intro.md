<properties
    pageTitle="Quel est l’automatisation d’Azure | Microsoft Azure"
    description="Découvrez quelle valeur Azure Automation fournit et obtenez les réponses aux questions les plus courantes afin que vous pouvez commencer à créer, à l’aide des procédures opérationnelles et Azure Automation DSC."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""
    keywords="Nouveautés, automation, automation azure, exemples d’automatisation azure"/>
<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article" 
    ms.date="05/10/2016"
    ms.author="magoedte;bwren"/>

# <a name="azure-automation-overview"></a>Vue d’ensemble de Automation Azure

Microsoft Azure Automation permet aux utilisateurs d’automatiser les tâches manuelles, longue, sujette aux erreurs et souvent répétées qui sont couramment exécutés dans un environnement de cloud et d’entreprise. Il fait gagner du temps et accroît la fiabilité des tâches administratives régulières et même les planifie à exécuter automatiquement à intervalles réguliers. Vous pouvez automatiser le processus à l’aide de procédures opérationnelles ou automatiser la gestion de la configuration à l’aide de la Configuration de l’état souhaité. Cet article fournit une vue d’ensemble succincte de l’automatisation d’Azure et répond à certaines questions courantes. Vous pouvez faire référence à d’autres articles de cette bibliothèque pour des informations plus détaillées sur les différentes rubriques.


## <a name="automating-processes-with-runbooks"></a>Automatisation des processus avec les procédures opérationnelles

Une procédure opérationnelle est un ensemble de tâches qui exécutent un processus automatisé dans Azure Automation. Il peut être un processus simple comme démarrer un ordinateur virtuel et la création d’une entrée de journal, ou vous pouvez avoir une procédure opérationnelle complexe qui combine d’autres procédures plus petites opérationnelles pour effectuer un processus complexe sur plusieurs ressources ou même plusieurs nuages et sur les environnements de site.  

Par exemple, vous pourriez avoir un processus manuel existant de la troncation d’une base de données SQL si elle approche de taille maximale qui comprend plusieurs étapes comme la connexion au serveur, se connecter à la base de données, obtenir la taille actuelle de la base de données, vérifier si le seuil est dépassé puis tronqué et avertir l’utilisateur. Au lieu d’exécuter manuellement chacune de ces étapes, vous pouvez créer une procédure opérationnelle qui effectue toutes ces tâches en un seul processus. Vous démarrez la procédure opérationnelle, fournissez les informations requises, telles que le nom du serveur SQL, nom de la base de données et messagerie du destinataire et sont lors de la fin du processus. 


## <a name="what-can-runbooks-automate"></a>Les procédures opérationnelles peuvent automatiser ?

Procédures opérationnelles dans Azure Automation sont basés sur Windows PowerShell ou de flux de travail Windows PowerShell, leur faire tout ce que PowerShell peut faire. Si une application ou un service dispose d’une API, une procédure opérationnelle peut fonctionner avec lui. Si vous avez un module PowerShell de l’application, vous pouvez charger ce module dans Azure Automation et inclure ces applets de commande dans votre procédure opérationnelle. Procédures opérationnelles de Automation Azure exécutent dans le nuage Azure et accéder aux ressources de cloud et les ressources externes qui sont accessibles à partir du cloud. Procédures opérationnelles à l’aide de [Travailleur de procédure opérationnelle hybride](automation-hybrid-runbook-worker.md), peuvent exécuter dans votre centre de données local pour gérer les ressources locales. 


## <a name="getting-runbooks-from-the-community"></a>Mise en route des procédures opérationnelles de la Communauté

La [Galerie de procédure opérationnelle](automation-runbook-gallery.md#runbooks-in-runbook-gallery) contient des procédures opérationnelles à partir de Microsoft et de la Communauté que vous pouvez utiliser telles quelles dans votre environnement, ou les personnaliser selon vos besoins. Ils sont également utiles pour en tant que références pour apprendre à créer vos propres procédures opérationnelles. Vous pouvez même contribuer vos propres procédures opérationnelles à la galerie que vous pensez que d’autres utilisateurs peuvent être utile. 


## <a name="creating-runbooks-with-azure-automation"></a>Création des procédures opérationnelles avec Automation Azure 

Vous pouvez [créer vos propres procédures opérationnelles](automation-creating-importing-runbook.md) à partir de zéro ou de modifier les procédures opérationnelles à partir de la [Galerie de procédure opérationnelle](http://msdn.microsoft.com/library/azure/dn781422.aspx) pour vos propres besoins. Il existe trois [types de procédure opérationnelle](automation-runbook-types.md) que vous pouvez choisir en fonction de vos exigences et une expérience de PowerShell. Si vous préférez travailler directement avec le code PowerShell, vous pouvez utiliser une [procédure opérationnelle de PowerShell](automation-runbook-types.md#powershell-runbooks) ou d’une [procédure opérationnelle de PowerShell le flux de travail](automation-runbook-types.md#powershell-workflow-runbooks) que vous modifiez en mode hors connexion, ou avec l' [éditeur de texte](http://msdn.microsoft.com/library/azure/dn879137.aspx) dans le portail Azure. Si vous préférez modifier une procédure opérationnelle sans être exposé au code sous-jacent, vous pouvez créer une [procédure opérationnelle de graphique](automation-runbook-types.md#graphical-runbooks) à l’aide de l' [éditeur de graphique](automation-graphical-authoring-intro.md) dans le portail Azure. 

Vous préférez regarder à lecture ? Examinez le dessous de vidéo à partir de la session de Microsoft Ignite en mai 2015. Remarque : Alors que les concepts et les fonctionnalités abordés dans cette vidéo sont correctes, Qu'azure Automation a beaucoup progressé depuis cette vidéo a été enregistrée, elle maintenant a une interface utilisateur plus étendue dans le portail Azure et prend en charge des fonctionnalités supplémentaires.

> [AZURE.VIDEO microsoft-ignite-2015-automating-operational-and-management-tasks-using-azure-automation]


## <a name="automating-configuration-management-with-desired-state-configuration"></a>Automatisation de la gestion de la configuration avec la Configuration de l’état souhaité 

[PowerShell DSC](https://technet.microsoft.com/library/dn249912.aspx) est une plate-forme de gestion qui vous permet de gérer, déployer et appliquer la configuration pour les hôtes physiques et les machines virtuelles à l’aide d’une syntaxe déclarative de PowerShell. Vous pouvez définir des configurations central DSC extraire sur un serveur sur les ordinateurs cible peuvent automatiquement récupérer et appliquer. DSC fournit un jeu d’applets de commande PowerShell que vous pouvez utiliser pour gérer les configurations et les ressources.  

[DSC d’Automation Azure](automation-dsc-overview.md) est une solution de nuage basé pour DSC PowerShell qui fournit les services requis pour les environnements d’entreprise.  Vous pouvez gérer vos ressources de DSC dans Azure Automation et appliquer des configurations d’ordinateurs virtuels ou physiques qui les récupérer à partir d’un serveur d’extraction de DSC dans le nuage Azure.  Il fournit également des services de création de rapports qui vous informent que des événements importants, tels que lorsque les nœuds sont sorties à partir de leur configuration affectée et lorsqu’une nouvelle configuration a été appliquées. 


## <a name="creating-your-own-dsc-configurations-with-azure-automation"></a>Création de vos propres configurations DSC avec Azure Automation

[Configurations de DSC](automation-dsc-overview.md#azure-automation-dsc-terms) spécifier l’état souhaité d’un nœud.  Plusieurs nœuds peuvent appliquer la même configuration pour vous assurer qu’ils conservent un état identique.  Vous pouvez créer une configuration à l’aide de n’importe quel texte d’éditeur sur votre ordinateur local et l’importer dans Automation Azure où vous pouvez le compiler et l’appliquer nœuds.


## <a name="getting-modules-and-configurations"></a>Lors de l’obtention des modules et des configurations 

Vous pouvez obtenir les [modules PowerShell](automation-runbook-gallery.md#modules-in-powershell-gallery) contenant des applets de commande que vous pouvez utiliser dans vos procédures opérationnelles et les configurations de DSC à partir de la [Galerie de PowerShell](http://www.powershellgallery.com/). Vous pouvez lancer cette galerie à partir du portail Azure et importer des modules directement dans Azure Automation, ou vous pouvez les télécharger et les importer manuellement. Vous ne pouvez pas installer les modules directement depuis le portail Azure, mais vous pouvez les télécharger les installer comme vous le feriez pour tout autre module. 


## <a name="example-practical-applications-of-azure-automation"></a>Exemple d’applications pratiques de l’automatisation d’Azure 

Voici quelques exemples de ce que sont les types de scénarios d’automatisation Automation d’Azure. 

* Créer et copier des ordinateurs virtuels dans les différentes souscriptions Azure. 
* Fichier du programme de copie à partir d’un ordinateur local à un conteneur de stockage des objets Blob Azure. 
* Automatiser les fonctions de sécurité telles que le refus demande à partir d’un client lors de la détection d’une attaque par déni de service. 
* Assurez-vous d’alignent en permanence les machines avec la stratégie de sécurité configurée.
* Gérer un déploiement continu de code de l’application sur le nuage et sur l’infrastructure du site. 
* Créer une forêt Active Directory dans Azure pour votre environnement de laboratoire. 
* Tronquer une table dans une base de données SQL si la base de données approche de taille maximale. 
* Mise à jour à distance des paramètres d’environnement pour un site Web d’Azure. 


## <a name="how-does-azure-automation-relate-to-other-automation-tools"></a>Comment se Azure Automation par rapport à d’autres outils d’automation ?

[Automatisation de gestion de service (SMA)](http://technet.microsoft.com/library/dn469260.aspx) est conçu pour automatiser les tâches de gestion dans le nuage privé. Il est installé localement dans votre centre de données en tant que composant du [Pack de Microsoft Azure](https://www.microsoft.com/en-us/server-cloud/). SMA et Azure Automation utilisent le même format de procédure opérationnelle basé sur Windows PowerShell et de Workflow de Windows PowerShell, mais le SMA ne prend pas en charge les [procédures opérationnelles de graphique](automation-graphical-authoring-intro.md).  

[System Center 2012 Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) est conçu pour l’automatisation des ressources de locaux. Il utilise un format différent de procédure opérationnelle à Automation d’Azure et d’automatisation de la gestion Service et dispose d’une interface graphique pour créer des procédures opérationnelles sans avoir besoin d’écrire un script. Ses procédures opérationnelles sont composés d’activités à partir des Packs d’intégration qui sont écrites spécifiquement pour Orchestrator. 


## <a name="where-can-i-get-more-information"></a>Où puis-je obtenir plus d’informations ? 

De nombreuses ressources sont disponibles pour vous pour plus d’informations sur Automation d’Azure et la création de vos propres procédures opérationnelles. 

* **Bibliothèque d’Automation Azure** est où vous vous trouvez maintenant. Les articles de cette bibliothèque fournissent une documentation complète sur la configuration et l’administration de l’automatisation d’Azure et pour la création de vos propres procédures opérationnelles. 
* [Applets de commande PowerShell de Azure](http://msdn.microsoft.com/library/jj156055.aspx) fournit des informations pour l’automatisation des opérations Azure à l’aide de Windows PowerShell. Procédures opérationnelles permet de travailler avec des ressources Azure ces applets de commande. 
* [Blog de gestion](https://azure.microsoft.com/blog/tag/azure-automation/) fournit les dernières informations sur Automation d’Azure et d’autres technologies de gestion de Microsoft. Vous devez vous abonner à ce blog pour rester à jour avec la dernière de l’équipe Azure Automation. 
* [Forum d’Automation](http://go.microsoft.com/fwlink/p/?LinkId=390561) vous permet de vous permet de publier des questions sur l’automatisation d’Azure pour être contactée par Microsoft et la Communauté d’Automation. 
* [Applets de commande Azure Automation](https://msdn.microsoft.com/library/mt244122.aspx) fournit des informations pour l’automatisation des tâches d’administration. Il contient des applets de commande pour gérer les comptes de l’automatisation, les actifs, les procédures opérationnelles, DSC.


## <a name="can-i-provide-feedback"></a>Puis-je fournir des commentaires ? 

**Faites-nous part de vos commentaires !** Si vous recherchez une solution de procédure opérationnelle Azure Automation ou d’un module d’intégration, valider une demande de Script sur le centre de scripts. Si vous avez des demandes de fonctionnalité de commentaires ou d’automatisation d’Azure, placez-les sur la [Voix de l’utilisateur](http://feedback.windowsazure.com/forums/34192--general-feedback). Merci ! 


