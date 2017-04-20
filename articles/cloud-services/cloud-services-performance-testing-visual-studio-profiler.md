<properties 
    pageTitle="Profilage d’un Service Cloud localement dans l’émulateur de calcul | Microsoft Azure" 
    services="cloud-services"
    description="Examinez les problèmes de performances dans les services en nuage avec le profileur Visual Studio" 
    documentationCenter=""
    authors="TomArcher" 
    manager="douge" 
    editor=""
    tags="" 
    />

<tags 
    ms.service="cloud-services" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="07/30/2016" 
    ms.author="tarcher"/>

# <a name="testing-the-performance-of-a-cloud-service-locally-in-the-azure-compute-emulator-using-the-visual-studio-profiler"></a>Test de la Performance d’un Service Cloud localement dans l’émulateur de calcul Azure le Générateur de profils Visual Studio

Une variété d’outils et de techniques sont disponibles pour tester les performances des services en nuage.
Lorsque vous publiez un service cloud sur Azure, vous pouvez avoir Visual Studio de collecter des données de profilage et analyser localement, comme décrit dans le [profilage d’une Application Azure][1].
Vous pouvez également utiliser des tests de diagnostic pour effectuer le suivi d’une variété de compteurs de performance, comme décrit dans [utilisation des compteurs de performance dans Azure][2].
Vous pourriez également profiler votre application localement dans l’émulateur de calcul avant de le déployer dans le nuage.

Cet article décrit la méthode d’échantillonnage de l’UC de profilage, qui peut être effectuée localement dans l’émulateur. Échantillonnage de l’UC est une méthode de profilage qui n’est pas très intrusive. À un intervalle d’échantillonnage désigné, le profileur prend un instantané de la pile des appels. Les données sont collectées sur une période de temps et affichées dans un rapport. Cette méthode de profilage a tendance à indiquer où dans une application de consommer beaucoup de l’essentiel du travail du processeur est effectuée.  Cela vous donne la possibilité de se concentrer sur le « chemin réactif » où votre application passe le plus de temps.



## <a name="1-configure-visual-studio-for-profiling"></a>1 : configurer Visual Studio pour le profilage

Tout d’abord, il y a quelques options de configuration de Visual Studio qui peuvent être utiles lors du profilage. Pour cerner le rapports de profilage, vous aurez besoin de symboles (fichiers .pdb) pour votre application, ainsi que les symboles pour les bibliothèques système. Vous voudrez vous assurer que vous référencez les serveurs de symboles disponibles. Pour ce faire, dans le menu **Outils** dans Visual Studio, choisissez **Options**, puis choisissez **débogage**, puis les **symboles**. Assurez-vous que les serveurs de symboles Microsoft est répertorié sous les **fichiers de symboles (.pdb)**.  Vous pouvez également référencer http://referencesource.microsoft.com/symbols, qui peuvent comporter des fichiers de symboles supplémentaires.

![Options de symbole][4]

Si vous le souhaitez, vous pouvez simplifier les rapports que le profileur génère en définissant uniquement mon Code. Avec uniquement mon Code est activé, les piles d’appels de fonction sont simplifiées afin que les appels entièrement internes pour le.NET Framework et les bibliothèques sont masqués pour les rapports. Dans le menu **Outils** , choisissez **Options**. Développez le nœud **Outils de Performance** , puis cliquez sur **Général**. Cochez la case pour **Activer uniquement mon Code pour les rapports du profileur**.

![Options de mon Code uniquement][17]

Vous pouvez utiliser ces instructions avec un projet existant ou un nouveau projet.  Si vous créez un nouveau projet pour essayer les techniques décrites ci-dessous, choisissez un projet C# **Azure Cloud Service** et sélectionnez un **Rôle Web** et un **Rôle Worker**.

![Rôles de projet de Service Cloud Azure][5]

Par exemple, à des fins, ajouter du code à votre projet qui prend beaucoup de temps et montre un problème de performance évidents. Par exemple, ajoutez le code suivant à un projet de rôle de travail :

    public class Concatenator
    {
        public static string Concatenate(int number)
        {
            int count;
            string s = "";
            for (count = 0; count < number; count++)
            {
                s += "\n" + count.ToString();
            }
            return s;
        }
    }

Appelez ce code à partir de la méthode RunAsync dans la classe dérivée de RoleEntryPoint du rôle travailleur. (Ignorez l’avertissement concernant la méthode à exécuter de façon synchrone.)

        private async Task RunAsync(CancellationToken cancellationToken)
        {
            // TODO: Replace the following with your own logic.
            while (!cancellationToken.IsCancellationRequested)
            {
                Trace.TraceInformation("Working");
                Concatenator.Concatenate(10000);
            }
        }

Générez et exécutez votre service cloud localement sans débogage (Ctrl + F5), avec la configuration de solution à **Release**. Cela garantit que tous les fichiers et les dossiers sont créés pour l’application s’exécute et permet de s’assurer que tous les émulateurs sont démarrés. Démarrez l’interface d’émulateur de calcul à partir de la barre des tâches pour vérifier que votre rôle de travail est en cours d’exécution.

## <a name="2-attach-to-a-process"></a>2 : attacher à un processus

Au lieu de profilage de l’application en le démarrant à partir de l’environnement IDE de Visual Studio 2010, vous devez attacher le profileur à un processus en cours d’exécution. 

Pour attacher le profileur à un processus, dans le menu **analyser** , cliquez sur **Générateur de profils** et **Attacher/Détacher**.

![Joindre l’option de profil][6]

Pour un rôle worker, recherchez le processus de WaWorkerHost.exe.

![Processus de WaWorkerHost][7]

Si votre dossier de projet se trouve sur un lecteur réseau, le profileur vous demandera de fournir un autre emplacement pour enregistrer les rapports de profilage.

 Vous pouvez également joindre à un rôle web en s’attachant à WaIISHost.exe.
S’il existe plusieurs processus de travail rôle dans votre application, vous devez utiliser la valeur processID pour les distinguer. Vous pouvez interroger la valeur processID par programme en accédant à l’objet de processus. Par exemple, si vous ajoutez ce code à la méthode Run de la classe dérivée de RoleEntryPoint dans un rôle, vous pouvez consulter le journal dans l’interface d’émulateur Compute de savoir quel processus pour se connecter à.

    var process = System.Diagnostics.Process.GetCurrentProcess();
    var message = String.Format("Process ID: {0}", process.Id);
    Trace.WriteLine(message, "Information");

Pour afficher le journal, démarrez l’interface d’émulateur de calcul.

![Démarrer l’émulateur de calcul l’interface utilisateur][8]

Ouvrez la fenêtre de console du journal de rôle Collaborateur dans l’interface utilisateur émulateur de calcul en cliquant sur dans la barre de titre de la fenêtre de console. Vous pouvez afficher l’ID de processus dans le journal.

![Afficher les ID de processus][9]

Une vous avez joint, effectuez les opérations dans l’interface utilisateur de votre application (si nécessaire) pour reproduire le scénario.

Lorsque vous souhaitez arrêter le profilage, cliquez sur le lien **Arrêter le profilage** .

![Arrêter le profilage d’option][10]

## <a name="3-view-performance-reports"></a>3 : permet d’afficher les rapports de performances

Le rapport de performances de votre application s’affiche.

À ce stade, le profileur interrompt, enregistre les données dans un fichier .vsp et affiche un rapport qui présente une analyse de ces données.

![Rapport du profileur][11]


Si vous voyez les String.wstrcpy dans le chemin d’accès rapide, cliquez sur uniquement mon Code pour modifier l’affichage pour afficher uniquement le code utilisateur.  Si vous voyez String.Concat, essayez d’appuyer sur le bouton Afficher tout le Code.

Vous devriez voir la méthode de concaténation et String.Concat occuper une grande partie de la durée d’exécution.

![Analyse d’état][12]

Si vous avez ajouté le code de concaténation de chaîne dans cet article, vous devez voir un message d’avertissement dans la liste des tâches pour cela. Vous pouvez également voir un message d’avertissement qu’il existe une quantité excessive de garbage collection, qui est le nombre de chaînes qui sont créés et supprimés.

![Avertissements liés aux performances][14]

## <a name="4-make-changes-and-compare-performance"></a>4 : apporter des modifications et de comparer les performances

Vous pouvez également comparer les performances avant et après une modification du code.  Arrêter le processus en cours d’exécution et modifiez le code pour remplacer l’opération de concaténation de chaîne à l’aide de StringBuilder :

    public static string Concatenate(int number)
    {
        int count;
        System.Text.StringBuilder builder = new System.Text.StringBuilder("");
        for (count = 0; count < number; count++)
        {
             builder.Append("\n" + count.ToString());
        }
        return builder.ToString();
    }

Faire une autre exécution de performances et ensuite comparer les performances. Dans l’Explorateur de performances, si les séries sont dans la même session, vous pouvez simplement sélectionner les deux rapports, ouvrir le menu contextuel et choisissez **Comparer les rapports de performances**. Si vous souhaitez comparer à une exécution dans une autre session de performance, ouvrez le menu **analyser** et choisissez **Comparer les rapports de performances**. Dans la boîte de dialogue qui s’affiche, spécifiez les deux fichiers.

![Comparer l’option des rapports de performances][15]

Les rapports de mettre en surbrillance les différences entre les deux séries.

![Rapport de comparaison][16]

Félicitations ! Vous avez obtenu lancé avec le profileur.

## <a name="troubleshooting"></a>Résolution des problèmes

- Assurez-vous que vous profilez une version Release et démarrez sans débogage.

- Si l’option Attacher/Détacher n’est pas activée dans le menu du Générateur de profils, exécutez l’Assistant Performance.

- Utilisez l’interface d’émulateur de calcul pour afficher l’état de votre application. 

- Si vous rencontrez des problèmes de démarrage des applications dans l’émulateur ou attachement du profileur, arrêter l’émulateur de calcul vers le bas et redémarrez-le. Si cela ne résout pas le problème, essayez de redémarrer. Ce problème peut se produire si vous utilisez l’émulateur de calcul de suspendre et de supprimer les déploiements en cours d’exécution.

- Si vous avez utilisé les commandes de profilage à partir de la ligne de commande, en particulier dans les paramètres globaux, assurez-vous que VSPerfClrEnv /globaloff a été appelée et que VsPerfMon.exe est arrêté.

- Si lors de l’échantillonnage, vous voyez le message « PRF0025 : aucune donnée n’a été collectée, » vérifiez que le processus que vous avez associé au dispose d’activité de l’UC. Les applications qui ne font pas tout travail informatique peut ne pas produisent des données d’échantillonnage.  Il est également possible que le processus s’est terminé avant tout prélèvement a été effectué. Vérifiez que la méthode Run pour un rôle que vous profilez ne s’arrête pas.

## <a name="next-steps"></a>Étapes suivantes

Le profileur Visual Studio ne prend pas en charge l’instrumentation de binaires Azure dans l’émulateur, mais si vous souhaitez tester l’allocation de mémoire, vous pouvez choisir cette option lors du profilage. Vous pouvez également choisir de profilage d’accès concurrentiel, qui vous permet de déterminer si les threads sont perdre du temps à concurrence de verrous ou de profilage d’interaction de couche, qui vous permet de suivre les problèmes de performance lors de l’interaction entre les différents niveaux d’une application, fréquemment entre la couche données et un rôle worker.  Vous pouvez afficher les requêtes de base de données que votre application génère et utiliser les données de profilage pour améliorer votre utilisation de la base de données. Pour plus d’informations sur le profilage d’interaction de couche, consultez le blog [procédure pas à pas : utilisation du profileur de Interaction de couche dans Visual Studio Team System 2010][3].



[1]: http://msdn.microsoft.com/library/azure/hh369930.aspx
[2]: http://msdn.microsoft.com/library/azure/hh411542.aspx
[3]: http://blogs.msdn.com/b/habibh/archive/2009/06/30/walkthrough-using-the-tier-interaction-profiler-in-visual-studio-team-system-2010.aspx
[4]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally09.png
[5]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally10.png
[6]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally02.png
[7]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally05.png
[8]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally010.png
[9]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally07.png
[10]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally06.png
[11]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally03.png
[12]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally011.png
[14]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally04.png 
[15]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally013.png
[16]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally012.png
[17]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally08.png
 