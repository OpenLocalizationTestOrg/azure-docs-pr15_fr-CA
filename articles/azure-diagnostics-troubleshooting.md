<properties
    pageTitle="Résolution des problèmes de Diagnostics de Windows Azure"
    description="Résoudre les problèmes lors de l’utilisation des diagnostics de Windows Azure dans Azure Cloud Services, ordinateurs virtuels et "
    services="multiple"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="robb"/>


# <a name="azure-diagnostics-troubleshooting"></a>Résolution des problèmes de Diagnostics de Windows Azure
Résolution des problèmes des informations pertinentes à l’aide des Diagnostics d’Azure. Pour plus d’informations sur les diagnostics de Windows Azure, consultez [Vue d’ensemble des Diagnostics Azure](azure-diagnostics.md#cloud-services).

## <a name="azure-diagnostics-is-not-starting"></a>Diagnostics de Windows Azure ne démarre pas

Tests de diagnostic est constitué de deux composants : un plug-in de l’agent invité et l’agent de surveillance. Vous pouvez vérifier les fichiers journaux **DiagnosticsPluginLauncher.log** et **DiagnosticsPlugin.log** pour plus d’informations sur les causes d’échec des tests de diagnostic Démarrer.  
  
Dans un rôle de Service en nuage, les fichiers journaux pour le plug-in de l’agent invité se trouvent dans : 

``` 
C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.6.3.0\ 
``` 

Dans une Machine virtuelle Azure, les fichiers journaux pour le plug-in de l’agent invité sont situés dans : 

``` 
C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.6.3.0\Logs\ 
``` 
 
La dernière ligne des fichiers journaux contient le code de sortie.  

``` 
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0 
``` 

Le plug-in renvoie les codes de sortie suivants :

Code de sortie|Description
---|---
0|Succès.
-1|Erreur générique.
-2|Impossible de charger le fichier rcf.<p>Il s’agit d’une erreur interne qui doit uniquement se produire si le Lanceur de plug-in de l’agent invité est appelé manuellement, de manière incorrecte, sur l’ordinateur virtuel.
-3|Impossible de charger le fichier de configuration de Diagnostics.<p><p>Solution : Ceci est le résultat d’un fichier de configuration ne transmet ne pas la validation de schéma. La solution consiste à fournir un fichier de configuration qui est conforme au schéma.
-4|Le répertoire de ressources locales est déjà utilisé par une autre instance de l’agent de surveillance de diagnostic.<p><p>Solution : Spécifiez une autre valeur pour **LocalResourceDirectory**.
-6|Le Lanceur de plug-in de l’agent invité a tenté de lancer des Diagnostics avec une ligne de commande non valide.<p><p>Il s’agit d’une erreur interne qui doit uniquement se produire si le Lanceur de plug-in de l’agent invité est appelé manuellement, de manière incorrecte, sur l’ordinateur virtuel.
-10|Le plug-in Diagnostics fermé avec une exception non gérée.
-11|L’agent de l’invité n’a pas pu créer le processus responsable du lancement et de contrôle de l’agent de surveillance.<p><p>Solution : Vérifiez que les ressources système suffisantes sont disponibles pour le lancement de nouveaux processus.<p>
-101|Arguments non valides lors de l’appel du plug-in de Diagnostics.<p><p>Il s’agit d’une erreur interne qui doit uniquement se produire si le Lanceur de plug-in de l’agent invité est appelé manuellement, de manière incorrecte, sur l’ordinateur virtuel.
-102|Le processus de plug-in ne peut pas s’initialiser.<p><p>Solution : Vérifiez que les ressources système suffisantes sont disponibles pour le lancement de nouveaux processus.
-103|Le processus de plug-in ne peut pas s’initialiser. Plus précisément, il est impossible de créer l’objet logger.<p><p>Solution : Vérifiez que les ressources système suffisantes sont disponibles pour le lancement de nouveaux processus.
-104|Impossible de charger le fichier rcf fourni par le représentant de l’invité.<p><p>Il s’agit d’une erreur interne qui doit uniquement se produire si le Lanceur de plug-in de l’agent invité est appelé manuellement, de manière incorrecte, sur l’ordinateur virtuel.
-105|Le plug-in de tests de diagnostic ne peut pas ouvrir le fichier de configuration de Diagnostics.<p><p>Il s’agit d’une erreur interne qui doit uniquement se produire si le plug-in Diagnostics est appelé manuellement, de manière incorrecte, sur l’ordinateur virtuel.
-106|Impossible de lire le fichier de configuration de Diagnostics.<p><p>Solution : Ceci est le résultat d’un fichier de configuration ne transmet ne pas la validation de schéma. La solution consiste à fournir un fichier de configuration qui est conforme au schéma. Vous pouvez trouver le code XML qui est remis à l’extension de Diagnostics dans le dossier *%SystemDrive%\WindowsAzure\Config* sur l’ordinateur virtuel. Ouvrez le fichier XML et la recherche de **Microsoft.Azure.Diagnostics**, puis pour le champ **xmlCfg** appropriée. Les données sont codées afin que vous devez [le décoder](http://www.bing.com/search?q=base64+decoder) voir le code XML qui a été chargé par les tests de diagnostic en base64.<p>
-107|Le traitement du répertoire de ressources à l’agent de surveillance n’est pas valide.<p><p>Il s’agit d’une erreur interne qui doit uniquement se produire si l’agent de surveillance est appelée manuellement, de manière incorrecte, sur l’ordinateur virtuel.</p>
-108    |Impossible de convertir le fichier de configuration de Diagnostics dans le fichier de configuration de l’agent surveillance.<p><p>Il s’agit d’une erreur interne qui doit uniquement se produire si le plug-in Diagnostics est appelé manuellement avec un fichier de configuration non valide.
-110|Erreur de configuration de diagnostic général.<p><p>Il s’agit d’une erreur interne qui doit uniquement se produire si le plug-in Diagnostics est appelé manuellement avec un fichier de configuration non valide.
-111|Impossible de démarrer l’agent de surveillance.<p><p>Solution : Vérifiez que les ressources système nécessaires sont disponibles.
-112|Erreur générale


## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>Les données de diagnostic sont pas connecté au stockage Azure
Diagnostics de Windows Azure stocke toutes les données dans le stockage Azure.

La cause la plus courante de ne pas les données d’événement est définie de manière incorrecte de stockage les informations de compte.

Solution : Corriger votre fichier de configuration de Diagnostics et réinstallez les tests de diagnostic.
Si le problème persiste après avoir réinstallé l’extension des tests de diagnostic, puis vous devrez déboguer d’autres en parcourant les surveillance des erreurs de l’agent. Avant que les données d’événement sont téléchargées vers votre compte de stockage, il est stocké dans le LocalResourceDirectory.

Pour le rôle de Service de Cloud le LocalResourceDirectory est la suivante :

    C:\Resources\Directory\<CloudServiceDeploymentID>.<RoleName>.DiagnosticStore\WAD<DiagnosticsMajorandMinorVersion>\Tables

Pour les Machines virtuelles, le LocalResourceDirectory est :

    C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD<DiagnosticsMajorandMinorVersion>\Tables

S’il n’y a pas de fichiers dans le dossier LocalResourceDirectory, l’agent de surveillance ne peut pas lancer. Cela correspond généralement à un fichier de configuration non valide, un événement qui doit être signalé dans la CommandExecution.log.

Si l’Agent de surveillance parvient à collecter les données d’événement, vous verrez les fichiers .tsf pour chaque événement défini dans votre fichier de configuration. L’Agent de surveillance enregistre les erreurs dans le fichier MaEventTable.tsf. Pour inspecter le contenu de ce fichier, vous pouvez utiliser l’application tabel2csv pour convertir le fichier .tsf dans un fichier de values(.csv) séparées par des virgules :

Un rôle de Service Cloud :

    %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<DiagnosticsVersion>\Monitor\x64\table2csv maeventtable.tsf

*% Lecteur_système %* sur un rôle de Service Cloud est généralement D:

Sur une Machine virtuelle :

    C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\Monitor\x64\table2csv maeventtable.tsf

Les commandes ci-dessus génère le fichier journal *maeventtable.csv*que vous pouvez ouvrir et inspecter les messages d’échec.    


## <a name="diagnostics-data-tables-not-found"></a>Données de diagnostic Tables non trouvés
Les tables de stockage Azure contenant des données de diagnostics de Windows Azure sont nommés à l’aide du code ci-dessous :

        if (String.IsNullOrEmpty(eventDestination)) {
            if (e == "DefaultEvents")
                tableName = "WADDefault" + MD5(provider);
            else
                tableName = "WADEvent" + MD5(provider) + eventId;
        }
        else
            tableName = "WAD" + eventDestination;

Voici un exemple :

        <EtwEventSourceProviderConfiguration provider=”prov1”>
          <Event id=”1” />
          <Event id=”2” eventDestination=”dest1” />
          <DefaultEvents />
        </EtwEventSourceProviderConfiguration>
        <EtwEventSourceProviderConfiguration provider=”prov2”>
          <DefaultEvents eventDestination=”dest2” />
        </EtwEventSourceProviderConfiguration>

Qui génère des 4 tables :

Événement|Nom de la table
---|---
fournisseur = « prov1 » &lt;l’id d’événement = « 1 » /&gt;|WADEvent + MD5("prov1") + « 1 »
fournisseur = « prov1 » &lt;l’id d’événement = « 2 » eventDestination = « dest1 » /&gt;|WADdest1
fournisseur = « prov1 » &lt;DefaultEvents /&gt;|WADDefault+MD5("prov1")
fournisseur = « prov2 » &lt;DefaultEvents eventDestination = « dest2 » /&gt;|WADdest2
