<properties
    pageTitle="Débogage d’Applications Azure dans Éclipse"
    description="Obtenir des informations sur le débogage Azure des Applications à l’aide de la Shared Computer Toolkit Azure pour Eclipse."
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

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690949.aspx -->

# <a name="debugging-azure-applications-in-eclipse"></a>Débogage d’Applications Azure dans Éclipse #

À l’aide de la Shared Computer Toolkit Azure pour Eclipse, vous pouvez déboguer vos applications si elles sont en cours d’exécution dans l’émulateur de calcul Azure ou si vous utilisez Windows comme système d’exploitation. L’image suivante montre les propriétés de **débogage** boîte de dialogue est utilisée pour activer le débogage distant :

![][ic719504]

Ce didacticiel suppose que vous disposez déjà d’une application qui a été créée avec succès et est familiarisés avec l’émulateur de calcul et du déploiement d’Azure.

Nous allons utiliser le didacticiel [à l’aide de la bibliothèque d’exécution de Service Azure dans JSP][] de l’application comme point de départ pour cette rubrique. Avant de continuer, créez cette application si vous ne le n'avez pas déjà fait.

## <a name="to-debug-your-application-while-running-in-azure"></a>Pour déboguer votre application lors de l’exécution dans Azure ##

>[AZURE.WARNING] Prise en charge actuelle de la boîte à outils pour le débogage distant de Java est conçu principalement pour les déploiements en cours d’exécution dans l’émulateur de calcul Azure. Étant donné que la connexion de débogage n’est pas sécurisée, vous n’activez pas le débogage distant dans les déploiements de production. Si vous devez déboguer une application s’exécutant dans le nuage Azure, utiliser un déploiement intermédiaire, mais que l’accès non autorisé à votre session de débogage est possible si quelqu'un connaît l’adresse IP de votre déploiement de cloud, même s’il s’agit d’un déploiement intermédiaire.

1. Générez votre projet de test dans l’émulateur : l’Explorateur dans Eclipse de projet, droit sur **MyAzureProject**et cliquez sur **Propriétés**, cliquez sur **Azure**la valeur **Build pour** **le déploiement vers le cloud**.
1. Régénérez votre projet : dans le menu Eclipse, cliquez sur **projet**, puis **Générer tous les**.
1. Déployer votre application à *la zone de transit* dans Azure.
    >[AZURE.IMPORTANT] Comme mentionné ci-dessus, il est fortement recommandé que vous déboguez dans l’émulateur de calcul dans la plupart des cas, puis déboguez dans l’environnement intermédiaire que si le débogage supplémentaire est nécessaire. Il est recommandé de ne déboguer pas dans l’environnement de production.
1. Une fois votre déploiement est prêt dans Azure, obtenir le nom DNS pour le déploiement à partir du [Portail de gestion Azure][]. Un déploiement intermédiaire a un nom DNS sous la forme http://*&lt;guid&gt;*. cloudapp.net, où * &lt;guid&gt; * est une valeur GUID assignée par Azure.
1. Dans l’Explorateur de projet de Eclipse, cliquez sur **WorkerRole1**et cliquez sur **Azure**puis cliquez sur **débogage**.
1. Dans la boîte de dialogue **Propriétés pour le débogage de WorkerRole1** :
    1. Vérifiez **Activer le débogage distant pour ce rôle.**
    1. De **point de terminaison d’entrée à utiliser**, utilisez le **débogage (public : 8090, privé : 8090)**.
    1. Assurez-vous de **Que Démarrer la machine virtuelle Java en mode suspendu, en attente d’une connexion du débogueur** est désactivée.
        >[AZURE.IMPORTANT] L’option **Démarrer la machine virtuelle Java en mode suspendu, en attente d’une connexion du débogueur** est destinée pour les avancées de débogage des scénarios dans l’émulateur de calcul uniquement (pas pour les déploiements dans le cloud). Si l’option **Démarrer la machine virtuelle Java en mode suspendu, en attente d’une connexion du débogueur** est utilisée, il suspend les processus de démarrage du serveur jusqu'à ce que le débogueur Eclipse est connecté à la machine virtuelle Java. Alors que vous pouvez utiliser cette option pour une session de débogage à l’aide de l’émulateur de calcul, ne l’utilisez pas pour une session de débogage dans un déploiement de cloud. L’initialisation d’un serveur s’effectue dans une tâche de démarrage Azure et le nuage Azure ne rend pas les points de terminaison publiques disponible jusqu'à ce que la tâche de démarrage est terminée. Par conséquent, un processus de démarrage s’achèvera pas si cette option est activée dans un déploiement de cloud, car il ne sera pas en mesure de recevoir une connexion depuis un client Eclipse externe.
1. Cliquez sur **créer des Configurations de débogage**.
1. Dans la boîte de dialogue **Configuration de débogage Azure** :
    1. **Projet Java à déboguer**, sélectionnez le projet **MyHelloWorld** .
    1. Pour le **configurer pour le débogage**, vérifiez **Azure cloud (intermédiaire)**.
    1. Vérifiez le **que émulateur de calcul Azure** n’est pas cochée.
    1. Pour l' **hôte**, entrez le nom DNS de votre déploiement par étapes, mais sans le précédent **http://**. Par exemple (utilisez votre GUID au lieu du GUID indiqué ici) : **65-6973-526f62657274.cloudapp.net 4e616d65-6f6e-6 d**
1. Cliquez sur **OK** pour fermer la boîte de dialogue de **Configuration Debug d’Azure** .
1. Cliquez sur **OK** pour fermer la boîte de dialogue **Propriétés pour le débogage de WorkerRole1** .
1. Si vous n’avez pas un point d’arrêt défini dans index.jsp, la valeur :
    1. Dans l’Explorateur de projet de Eclipse, développez **MyHelloWorld**, développer du **contenu Web**et double-cliquez sur **index.jsp**.
    1. Dans index.jsp, avec le bouton droit dans la barre bleue à gauche du code Java et cliquez sur les **Points d’arrêt de l’activer/désactiver**, comme illustré dans le code suivant : ![][ic551537]
1. Dans le menu de d’Eclipse, cliquez sur **exécuter** et puis cliquez sur **Debug Configurations**.
1. Dans la boîte de dialogue **Configurations de débogage** , développer **Des applications Java à distance** dans le volet gauche, sélectionnez **Azure Cloud (WorkerRole1)**, cliquez sur **Déboguer**.
1. Dans votre navigateur, exécutez votre application intermédiaire, **http://***&lt;guid&gt;***.cloudapp.net/MyHelloWorld**, en remplaçant le GUID de votre nom de DNS de * &lt;guid&gt;*. Si vous y êtes invité par une boîte de dialogue **Confirmer le commutateur du point de vue** , cliquez sur **Oui**. Votre session de débogage doit s’exécuter maintenant pour la ligne de code où le point d’arrêt a été défini.

>[AZURE.NOTE] Si vous tentez de démarrer un ordinateur connexion à un déploiement qui comporte plusieurs instances de rôle en cours d’exécution, vous ne pouvez pas contrôler actuellement de l’instance de débogage le débogueur sera initialement connecté, comme l’équilibreur de charge Azure choisit au hasard une instance. Une fois que vous êtes connecté à cette instance, cependant, vous continuerez la même instance de débogage. Remarque en outre, s’il existe une période d’inactivité de plus de 4 minutes (par exemple, lorsque vous êtes arrêté à un point d’arrêt pendant trop longtemps), Azure peut fermer la connexion.

## <a name="debugging-a-specific-role-instance-in-a-multi-instance-deployment"></a>Débogage d’une instance de rôle spécifique dans un déploiement de plusieurs instances ##

Lorsque le déploiement s’exécute dans le nuage, vous seront très probablement exécutées il plusieurs compute, ou des rôles, des instances. Cela vous permet de tirer parti de la garantie de disponibilité Azure 99,95 % et de faire évoluer votre application.

Dans ces scénarios, vous devrez peut-être déboguer à distance votre application Java dans une instance de rôle spécifique. Toutefois, si vous activez uniquement une régulière d’entrée point de terminaison pour le débogage, l’équilibreur de charge Azure rend pratiquement impossible de vous connecter le débogueur pour une instance de rôle spécifique. À la place il vous connectera à une instance de rôle qui il choisit de manière aléatoire.

Il s’agit du type de scénario où en tirant parti des points de terminaison d’entrée instance rend plus facile à déboguer une instance de rôle spécifique.

Supposons que vous envisagez d’exécuter jusqu'à 5 instances de rôle de votre déploiement. À l’aide de la page de propriétés des **points de terminaison** dans la boîte de dialogue de propriétés de rôle, créer un point de terminaison d’entrée d’instance et l’attribuer une plage de ports publics, plutôt que d’un seul numéro de port. Par exemple, dans la zone d’entrée de **port Public** , vous devez spécifier **81-85**.

Après avoir déployé votre application avec ce point de terminaison d’instance, Azure assignera un numéro de port unique à partir de cette plage à chacune des instances de rôle. Ensuite, pour savoir quelle instance a attribué le numéro de port, vous pouvez utiliser la variable d’environnement**_PUBLICPORT** *InstanceEndpointName*(où *InstanceEndpointName* est le nom que vous avez assigné lorsque vous avez créé le point de terminaison d’instance) configuré automatiquement par le Kit d’outils de votre déploiement (par exemple, en retournant sa valeur dans le pied de page d’une page Web, afin que vous pouvez le lire lorsque vous accédez à celui-ci).

Une fois que vous savez quel numéro de port publics cette instance a été affectée, vous pouvez référencer dans votre configuration de débogage dans Eclipse, par l’apposition au nom d’hôte de votre service. Cela permettra le débogueur Eclipse pour se connecter à cette instance spécifique et pas les autres instances.

## <a name="windows-only-to-debug-your-application-while-running-in-the-compute-emulator"></a>Windows uniquement : pour déboguer votre application lors de l’exécution dans l’émulateur de calcul ##

>[AZURE.NOTE] L’émulateur Azure n’est disponible que sous Windows. Ignorer cette section si vous utilisez un système d’exploitation autre que Windows. 

1. Générez votre projet de test dans l’émulateur : l’Explorateur dans Eclipse de projet, droit **MyAzureProject**et cliquez sur **Propriétés**, cliquez sur **Azure**, définissez la **génération de** test **dans l’émulateur**.
1. Régénérez votre projet : dans le menu Eclipse, cliquez sur **projet**, puis **Générer tous les**.
1. Dans l’Explorateur de projet de Eclipse, cliquez sur **WorkerRole1**et cliquez sur **Azure**puis cliquez sur **débogage**.
1. Dans la boîte de dialogue **Propriétés pour le débogage de WorkerRole1** :
    1. Vérifiez **Activer le débogage distant pour ce rôle.**
    1. De **point de terminaison d’entrée à utiliser**, utilisez le point de terminaison par défaut généré automatiquement par la boîte à outils, **débogage (public : 8090, privé : 8090)**dans la liste.
    1. Assurez-vous que l’option **Démarrer la machine virtuelle Java en mode suspendu, en attente d’une connexion du débogueur** est désactivée.
        >[AZURE.IMPORTANT] L’option **Démarrer la machine virtuelle Java en mode suspendu, en attente d’une connexion du débogueur** est destinée pour les avancées de débogage des scénarios dans l’émulateur de calcul uniquement (pas pour les déploiements dans le cloud). Si l’option **Démarrer la machine virtuelle Java en mode suspendu, en attente d’une connexion du débogueur** est utilisée, il suspend les processus de démarrage du serveur jusqu'à ce que le débogueur Eclipse est connecté à la machine virtuelle Java. Alors que vous pouvez utiliser cette option pour une session de débogage à l’aide de l’émulateur de calcul, ne l’utilisez pas pour une session de débogage dans un déploiement de cloud. L’initialisation d’un serveur s’effectue dans une tâche de démarrage Azure et le nuage Azure ne rend pas les points de terminaison publiques disponible jusqu'à ce que la tâche de démarrage est terminée. Par conséquent, un processus de démarrage s’achèvera pas si cette option est activée dans un déploiement de cloud, car il ne sera pas en mesure de recevoir une connexion depuis un client Eclipse externe.
1. Cliquez sur **créer des Configurations de débogage**.
1. Dans la boîte de dialogue **Configuration de débogage Azure** :
    1. **Projet Java à déboguer**, sélectionnez le projet **MyHelloWorld** .
    1. Pour **configurer le débogage pour**vérifier **l’émulateur de calcul Azure**.
1. Cliquez sur **OK** pour fermer la boîte de dialogue de **Configuration Debug d’Azure** .
1. Cliquez sur **OK** pour fermer la boîte de dialogue **Propriétés pour le débogage de WorkerRole1** .
1. Définissez un point d’arrêt dans index.jsp :
    1. Dans l’Explorateur de projet de Eclipse, développez **MyHelloWorld**, développer du **contenu Web**et double-cliquez sur **index.jsp**.
    1. Dans index.jsp, avec le bouton droit dans la barre bleue à gauche du code Java et cliquez sur les **Points d’arrêt de l’activer/désactiver**, comme illustré dans le code suivant : ![][ic551537]

       Un point d’arrêt est défini si vous voyez une icône de point d’arrêt dans la barre bleue à gauche du code Java.
1. Démarrez l’application dans l’émulateur de calcul en cliquant sur le bouton **exécuter dans l’émulateur d’Azure** dans la barre d’outils Azure.
1. Dans le menu de d’Eclipse, cliquez sur **exécuter** et puis cliquez sur **Debug Configurations**.
1. Dans la boîte de dialogue **Configurations de débogage** , développer **Des applications Java à distance** dans le volet gauche, sélectionnez **L’émulateur Azure (WorkerRole1)**, cliquez sur **Déboguer**.
1. Une fois que l’émulateur de calcul indique que votre application s’exécute dans votre navigateur, exécutez **Http://localhost : 8080/MyHelloWorld**.
    Si vous y êtes invité par une boîte de dialogue **Confirmer le commutateur du point de vue** , cliquez sur **Oui**.
    Votre session de débogage doit s’exécuter maintenant pour la ligne de code où le point d’arrêt a été défini.

Cela vous a montré comment déboguer dans l’émulateur de calcul ; la section suivante vous montre comment déboguer une application déployée sur Azure.

## <a name="debugging-notes"></a>Débogage des Notes ##

* Après le débogage, vous pouvez basculer le point de vue de **débogage** **Java** via en cliquant sur le menu de d’Eclipse, en cliquant sur **fenêtre**, **Du point de vue ouverte**, sélectionnez la perspective que vous souhaitez utiliser.
* Pour activer le débogage distant dans GlassFish, n’utilisez pas la fonctionnalité de configuration de débogage à distance de la Shared Computer Toolkit Azure pour Eclipse. Au lieu de cela, configurez manuellement GlassFish. En raison de la façon dont GlassFish traite les options Java prédéfinies dans les variables d’environnement, fonctionnalité de configuration de débogage à distance de la boîte à outils ne fonctionne pas correctement avec GlassFish. Si la fonctionnalité de configuration de débogage à distance de la boîte à outils est activée, il peut empêcher les GlassFish de démarrage.

## <a name="see-also"></a>Voir aussi ##

[Azure Shared Computer Toolkit pour Éclipse][]

[Création d’une Application du monde Hello pour Azure dans Éclipse][]

[L’installation de la Shared Computer Toolkit Azure pour Éclipse][] 

Pour plus d’informations sur l’utilisation d’Azure avec Java, consultez le [Centre pour développeurs Java Azure][].

<!-- URL List -->

[Centre de développement Java Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Portail de gestion Azure]: http://go.microsoft.com/fwlink/?LinkID=512959
[Azure Shared Computer Toolkit pour Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Création d’une Application du monde Hello pour Azure dans Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[L’installation de la Shared Computer Toolkit Azure pour Éclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[À l’aide de la bibliothèque Runtime de Service Azure dans JSP]: http://go.microsoft.com/fwlink/?LinkID=699551

<!-- IMG List -->

[ic719504]: ./media/azure-toolkit-for-eclipse-debugging-azure-applications/ic719504.png
[ic551537]: ./media/azure-toolkit-for-eclipse-debugging-azure-applications/ic551537.png
