<properties
   pageTitle="Migration d’Orchestrator vers Azure automatisation | Microsoft Azure"
   description="Décrit comment migrer des packs d’intégration et de procédures opérationnelles de System Center Orchestrator vers Azure Automation."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="bwren" />


# <a name="migrating-from-orchestrator-to-azure-automation-beta"></a>Migration d’Orchestrator vers Azure Automation (version bêta)

Procédures opérationnelles dans [System Center Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) sont basés sur les activités des packs d’intégration qui sont écrites spécifiquement pour Orchestrator procédures opérationnelles dans Azure Automation sont basés sur Windows PowerShell.  [Procédures opérationnelles graphique](automation-runbook-types.md#graphical-runbooks) dans Azure Automation ont une apparence semblable à des procédures opérationnelles d’Orchestrator avec leurs activités représentant les applets de commande PowerShell, des procédures opérationnelles des enfants et des actifs.

Le [System Center Orchestrator Migration Shared Computer Toolkit](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) inclut des outils pour vous aider à convertir des procédures opérationnelles d’Orchestrator Azure Automation.  En plus de convertir les procédures opérationnelles eux-mêmes, vous devez convertir les packs d’intégration avec les activités que les procédures opérationnelles utilisent les modules d’intégration avec les applets de commande Windows PowerShell.  

Voici le processus de base pour la conversion des procédures opérationnelles d’Orchestrator Automation d’Azure.  Chacune de ces étapes est décrite en détail dans les sections ci-dessous.

1.  Téléchargez [System Center Orchestrator Migration Shared Computer Toolkit](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) qui contient les outils et les modules abordés dans cet article.
2.  Importer [des activités Standard Module](#standard-activities-module) dans Azure Automation.  Cela inclut les versions converties des activités Orchestrator standard qui peuvent être utilisées par les procédures opérationnelles converti.
3.  Importez les [Modules d’intégration de System Center Orchestrator](#system-center-orchestrator-integration-modules) dans Azure Automation pour ces packs d’intégration utilisées par les procédures opérationnelles qui accèdent à System Center.
4.  Convertir des packs d’intégration personnalisés et tiers à l’aide du [Convertisseur de Pack d’intégration](#integration-pack-converter) et les importer dans Azure Automation.
5.  Convertir des procédures opérationnelles Orchestrator en utilisant le [Convertisseur de procédure opérationnelle](#runbook-converter) et installez dans Azure Automation.
6.  Créer manuellement des immobilisations Orchestrator requises dans Azure Automation dans la mesure où le convertisseur de procédure opérationnelle ne convertit pas ces ressources.
7.  Configurer un [Travail de procédure opérationnelle hybride](#hybrid-runbook-worker) dans votre centre de données local pour exécuter des procédures opérationnelles converti qui accédera aux ressources locales.

## <a name="service-management-automation"></a>Automatisation de la gestion service

[Automatisation de la gestion service](http://technet.microsoft.com/library/dn469260.aspx) (SMA) stocke et exécute les procédures opérationnelles de votre centre de données locales comme Orchestrator, et il utilise les mêmes modules d’intégration Azure Automation. Le [Convertisseur de procédure opérationnelle](#runbook-converter) convertit procédures opérationnelles d’Orchestrator procédures opérationnelles graphique mais qui ne sont pas pris en charge dans le SMA.  Vous pouvez toujours installer le [Module d’activités Standard](#standard-activities-module) et les [Modules d’intégration de System Center Orchestrator](#system-center-orchestrator-integration-modules) dans SMA, mais vous devez manuellement [réécrire les procédures opérationnelles](http://technet.microsoft.com/library/dn469262.aspx).

## <a name="hybrid-runbook-worker"></a>Travail de procédure opérationnelle hybride

Procédures opérationnelles d’Orchestrator sont stockés sur un serveur de base de données et sur les serveurs de procédure opérationnelle, à la fois dans votre centre de données local.  Procédures opérationnelles dans Azure Automation sont stockées dans le nuage Azure et peuvent s’exécuter dans votre centre de données local à l’aide d’un [Travail de procédure opérationnelle hybride](automation-hybrid-runbook-worker.md).  Voici comment vous s’exécute généralement des procédures opérationnelles converti à partir d’Orchestrator, dans la mesure où ils sont conçus pour fonctionner sur des serveurs locaux.

## <a name="integration-pack-converter"></a>Convertisseur de Pack d’intégration

Le convertisseur du Pack d’intégration des packs d’intégration qui ont été créés à l’aide de [Shared Computer Toolkit d’intégration Orchestrator (OIT)](http://technet.microsoft.com/library/hh855853.aspx) à des modules d’intégration basé sur Windows PowerShell qui peut être importé dans Azure Automation ou l’automatisation de la gestion Service.  

Lorsque vous exécutez le convertisseur de Pack d’intégration, vous sont présentées avec un Assistant qui vous permet de sélectionner un fichier de pack (.oip) d’intégration.  Ensuite, l’Assistant répertorie les activités incluses dans ce pack d’intégration et vous autorise à sélectionner qui seront migrés.  Lorsque vous exécutez l’Assistant, il crée un module d’intégration qui inclut une applet de commande correspondant pour chacune des activités dans le pack d’intégration d’origine.


### <a name="parameters"></a>Paramètres

Toutes les propriétés d’une activité dans le module d’intégration sont converties en paramètres de l’applet de commande correspondante dans le module d’intégration.  Les applets de commande Windows PowerShell ont un jeu de [paramètres communs](http://technet.microsoft.com/library/hh847884.aspx) qui peuvent être utilisées avec toutes les applets de commande.  Par exemple,-paramètre provoque une applet de commande Afficher les informations détaillées relatives à son fonctionnement.  Aucune cmdlet ne peut avoir un paramètre avec le même nom qu’un paramètre en commun.  Si une activité n’a pas une propriété portant le même nom qu’un paramètre commun, l’Assistant vous invitera à fournir un autre nom pour le paramètre.

### <a name="monitor-activities"></a>Surveillance des activités

Procédures opérationnelles du moniteur d’Orchestrator commencent par une [surveillance de l’activité](http://technet.microsoft.com/library/hh403827.aspx) et exécutent en permanence en attente d’être appelé par un événement particulier.  Automation Azure ne supporte pas les procédures opérationnelles du moniteur, afin que toute activité du moniteur dans le pack d’intégration ne sera pas convertie.  Au lieu de cela, une applet de commande espace réservé est créé dans le module d’intégration pour la surveillance de l’activité.  Cette applet de commande n’a aucune fonctionnalité, mais elle permet toute procédure opérationnelle converti qui l’utilise doit être installé.  Cette procédure opérationnelle ne sera pas en mesure d’exécuter dans l’automatisation d’Azure, mais il peut être installé pour que vous puissiez le modifier.

### <a name="integration-packs-that-cannot-be-converted"></a>Packs d’intégration qui ne peut pas être convertis.

Packs d’intégration qui n’ont pas été créés avec l’OIT ne peut pas être convertis avec un convertisseur du Pack de l’intégration. Il existe également certains packs d’intégration fournis par Microsoft qui ne peut pas actuellement être converti avec cet outil.  Converti les versions de ces packs d’intégration ont été [téléchargée](#system-center-orchestrator-integration-modules) afin qu’ils peuvent être installés dans Azure Automation ou l’automatisation de gestion de Service.


## <a name="standard-activities-module"></a>Module d’activités standard

Orchestrator comprend un ensemble d' [activités standard](http://technet.microsoft.com/library/hh403832.aspx) qui ne sont pas inclus dans un pack d’intégration, mais qui sont utilisés par un grand nombre de procédures opérationnelles.  Le module d’activités Standard est un module d’intégration qui inclut une applet de commande équivalente pour chacune de ces activités.  Vous devez installer le module d’intégration dans Azure Automation avant d’importer les procédures opérationnelles convertie qui utilisent une activité standard.

En plus de la prise en charge des procédures opérationnelles converti, les applets de commande dans le module des activités standard utilisable par une personne connaissant Orchestrator pour générer des procédures opérationnelles de nouveau dans Azure Automation.  Alors que les fonctionnalités de toutes les activités standard peuvent être effectuées avec les applets de commande, ils peuvent fonctionner différemment.  Les applets de commande dans le module des activités standard converti sera fonctionnent de la même manière que leurs activités correspondantes et utiliser les mêmes paramètres.  Cela peut aider l’auteur de procédure opérationnelle Orchestrator existant dans leur transition vers les procédures opérationnelles d’Automation d’Azure.

## <a name="system-center-orchestrator-integration-modules"></a>Modules d’intégration de System Center Orchestrator

Microsoft fournit des [packs d’intégration](http://technet.microsoft.com/library/hh295851.aspx) pour la création des procédures opérationnelles afin d’automatiser les composants de System Center et d’autres produits.  Certaines de ces packs d’intégration sont actuellement basés sur OIT mais ne peut pas actuellement être converties pour des modules d’intégration en raison de problèmes connus.  [Modules d’intégration de System Center Orchestrator](https://www.microsoft.com/download/details.aspx?id=49555) inclut des versions converties de ces packs d’intégration qui peuvent être importés dans Azure Automation et de l’automatisation de gestion de Service.  

Par la version RTM de cet outil, des versions mises à jour des packs d’intégration basés sur OIT qui peut être converti par le convertisseur de Pack d’intégration seront publiées.  Conseils sont fournis pour vous aider à convertir les procédures opérationnelles à l’aide des activités à partir des packs d’intégration non basées sur l’OIT.

## <a name="runbook-converter"></a>Convertisseur de procédure opérationnelle

Le convertisseur de procédure opérationnelle convertit les procédures opérationnelles d’Orchestrator des [procédures opérationnelles graphique](automation-runbook-types.md#graph-runbooks) qui peut être importé dans Azure Automation.  

Convertisseur de procédure opérationnelle est implémenté sous la forme d’un module PowerShell avec une applet de commande appelée **ConvertFrom-SCORunbook** qui effectue la conversion.  Lorsque vous installez l’outil, il crée un raccourci vers une session PowerShell qui charge l’applet de commande.   

Voici le processus de base pour convertir une procédure opérationnelle d’Orchestrator et importez-le dans Azure Automation.  Les sections suivantes fournissent plus de détails sur l’utilisation de l’outil et l’utilisation de procédures opérationnelles converti.

1. Exporter une ou plusieurs de procédures opérationnelles de Orchestrator.
2. Obtenir des modules d’intégration pour toutes les activités de la procédure opérationnelle.
3. Convertir les procédures opérationnelles d’Orchestrator dans le fichier exporté.
4. Examinez les informations dans les journaux pour valider la conversion et pour déterminer les tâches manuelles nécessaires.
4. Importer des procédures opérationnelles converti dans Azure Automation.
5. Créer tous les éléments requis dans Azure Automation.
6. Modifier la procédure opérationnelle dans Automation Azure pour modifier toutes les activités requises.

### <a name="using-runbook-converter"></a>À l’aide du convertisseur de procédure opérationnelle

La syntaxe de **ConvertFrom-SCORunbook** est la suivante :

    ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string> 

- RunbookPath - chemin d’accès au fichier d’exportation contenant les procédures opérationnelles à convertir.
- Module - virgule liste délimitée par des modules d’intégration contenant des activités dans les procédures opérationnelles.
- OutputFolder - chemin d’accès au dossier pour créer converti les procédures opérationnelles de graphique. 


La commande suivante convertit les procédures opérationnelles dans un fichier d’exportation appelé **MyRunbooks.ois_export**.  Ces procédures opérationnelles utilisent des packs d’intégration Active Directory et de Data Protection Manager.

    ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks" 


### <a name="log-files"></a>Fichiers journaux

Le convertisseur de procédure opérationnelle crée les fichiers journaux suivants dans le même emplacement que la procédure opérationnelle converti.  Si les fichiers existent déjà, ils seront remplacés avec les informations de la dernière conversion.

| Fichier | Contenu |
|:---|:---|
| Convertisseur de procédure opérationnelle, Progress.log | Procédure détaillée de la conversion, y compris des informations pour chaque activité correctement convertie et avertissement pour chaque activité n’a ne pas convertie. |
| Convertisseur de procédure opérationnelle, Summary.log  | Résumé de la dernière conversion, y compris tous les avertissements et le suivi des tâches que vous devez effectuer telles que la création d’une variable requise pour la procédure opérationnelle converti.  |

### <a name="exporting-runbooks-from-orchestrator"></a>Exportation des procédures opérationnelles d’Orchestrator

Le convertisseur de procédure opérationnelle fonctionne avec un fichier d’exportation d’Orchestrator qui contient un ou plusieurs de procédures opérationnelles.  Il crée une procédure d’automatisation d’Azure correspondant opérationnelle pour chaque procédure d’Orchestrator opérationnelle dans le fichier d’exportation.  

Pour exporter une procédure opérationnelle d’Orchestrator, double-cliquez sur le nom de la procédure opérationnelle dans le Concepteur de procédure opérationnelle et sélectionnez **Exporter**.  Pour exporter un ensemble de procédures opérationnelles dans un dossier, double-cliquez sur le nom du dossier et sélectionnez **Exporter**.


### <a name="runbook-activities"></a>Activités de procédure opérationnelle

Le convertisseur de procédure opérationnelle convertit chaque activité dans la procédure opérationnelle Orchestrator à une activité correspondante dans Azure Automation.  Pour les activités qui ne peut pas être converties, une activité de l’espace réservé est créée dans la procédure opérationnelle avec le texte de l’avertissement.  Après avoir importé la procédure opérationnelle converti dans Azure Automation, vous devez remplacer une de ces activités avec les activités valides qui exécutent les fonctions requises.

Toutes les activités dans le [Module des activités Standard](#standard-activities-module) Orchestrator seront converties.  Il existe certaines activités Orchestrator standard mais ne sont pas dans ce module et ne sont pas converties.  Par exemple, **Envoyer un événement de plate-forme** n’a aucune Automation Azure équivalent dans la mesure où l’événement est spécifique à Orchestrator.

[Surveillance des activités](https://technet.microsoft.com/library/hh403827.aspx) ne sont pas convertis car il n’existe pas d’équivalent pour les dans Azure Automation.  Les exceptions sont la surveillance des activités de [conversion des packs d’intégration](#integration-pack-converter) qui seront convertis à l’activité de l’espace réservé.

Toute activité à partir d’un [pack d’intégration de convertir](#integration-pack-converter) est convertie si vous fournissez le chemin d’accès pour le module d’intégration avec le paramètre de **modules** .  Pour les Packs d’intégration System Center, vous pouvez utiliser les [Modules d’intégration de System Center Orchestrator](#system-center-orchestrator-integration-modules).


### <a name="orchestrator-resources"></a>Ressources d’Orchestrator

Le convertisseur de procédure opérationnelle convertit uniquement les procédures opérationnelles, pas autres ressources Orchestrator tels que des compteurs, des variables ou des connexions.  Les compteurs ne sont pas pris en charge dans Azure Automation.  Les connexions et les variables sont prises en charge, mais vous devez les créer manuellement.  Les fichiers journaux seront vous informe si la procédure opérationnelle nécessite ces ressources et spécifier les ressources correspondantes dont vous avez besoin pour créer dans Automation Azure pour la procédure converti opérationnelle fonctionner correctement.

Par exemple, une procédure opérationnelle peut utiliser une variable pour remplir une valeur particulière dans une activité.  La procédure opérationnelle converti convertit l’activité et spécifier un élément variable dans Azure Automation avec le même nom que la variable Orchestrator.  Cela est indiqué dans le fichier de **Convertisseur de procédure opérationnelle, Summary.log** qui est créé après la conversion.  Vous devrez créer manuellement ce capital variable dans Azure Automation, avant d’utiliser la procédure opérationnelle. 

 
### <a name="input-parameters"></a>Paramètres d’entrée

Procédures opérationnelles d’Orchestrator acceptent des paramètres d’entrée à l’activité **d’Initialiser les données** .  Si la procédure opérationnelle en cours de conversion inclut cette activité, un [paramètre d’entrée](automation-graphical-authoring-intro.md#runbook-input-and-output) dans la procédure opérationnelle Azure Automation est créé pour chaque paramètre de l’activité.  Une activité de [contrôle de Script de flux de travail](automation-graphical-authoring-intro.md#activities) est créée dans la procédure opérationnelle converti qui extrait et renvoie chaque paramètre.  Toutes les activités dans la procédure opérationnelle qui utilisent un paramètre d’entrée font référence à la sortie de cette activité.

Il est la raison que cette stratégie est utilisée pour mieux refléter la fonctionnalité dans la procédure opérationnelle Orchestrator.  Activités dans les nouvelles procédures opérationnelles de graphique doivent faire référence directement à l’entrée des paramètres à l’aide d’une source de données d’entrée de procédure opérationnelle.


### <a name="invoke-runbook-activity"></a>Appeler des activités de procédure opérationnelle

Procédures opérationnelles d’Orchestrator démarrer les autres procédures opérationnelles à l’activité **D’appel de procédure opérationnelle** . Si la procédure opérationnelle en cours de conversion inclut cette activité et que l’option **attendre la fin** est définie, une activité de procédure opérationnelle est créée pour lui dans la procédure opérationnelle converti.  Si l’option **attendre la fin** n’est pas définie, une activité de Script de flux de travail est créée par **AzureAutomationRunbook-démarrer** pour démarrer la procédure opérationnelle.  Après avoir importé la procédure opérationnelle converti dans Azure Automation, vous devez modifier cette activité avec les informations spécifiées dans l’activité.




## <a name="related-articles"></a>Articles connexes

- [System Center 2012 - Orchestrator](http://technet.microsoft.com/library/hh237242.aspx)
- [Automatisation de la gestion service](https://technet.microsoft.com/library/dn469260.aspx)
- [Travail de procédure opérationnelle hybride](automation-hybrid-runbook-worker.md)
- [Activités Standard Orchestrator](http://technet.microsoft.com/library/hh403832.aspx)
- [Téléchargez System Center Orchestrator Migration Shared Computer Toolkit](https://www.microsoft.com/en-us/download/details.aspx?id=47323)
 
