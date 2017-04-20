<properties 
    pageTitle="Débogage d’un service en nuage Azure ou une machine virtuelle dans Visual Studio | Microsoft Azure"
    description="Débogage d’un Service en nuage ou la Machine virtuelle dans Visual Studio"
    services="visual-studio-online"
    documentationCenter="na"
    authors="TomArcher"
    manager="douge"
    editor="" />
<tags 
    ms.service="visual-studio-online"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="08/15/2016"
    ms.author="tarcher" />

# <a name="debugging-an-azure-cloud-service-or-virtual-machine-in-visual-studio"></a>Débogage d’un service en nuage Azure ou une machine virtuelle dans Visual Studio

Visual Studio vous offre différentes options pour le débogage des services cloud Azure et les machines virtuelles.



## <a name="debug-your-cloud-service-on-your-local-computer"></a>Déboguer votre service cloud sur votre ordinateur local

Vous pouvez gagner du temps et d’argent à l’aide de l’Azure compute émulateur pour déboguer votre service cloud sur un ordinateur local. Par le débogage d’un service local avant de le déployer, vous pouvez améliorer la fiabilité et les performances sans devoir payer pour des temps de calcul. Toutefois, certaines erreurs se produisent uniquement lorsque vous exécutez un service cloud dans Azure lui-même. Vous pouvez déboguer ces erreurs si vous activez le débogage distant lorsque vous publiez votre service, puis attacherez le débogueur à une instance de rôle.

L’émulateur simule le service Azure Compute et s’exécute dans votre environnement local afin que vous pouvez tester et déboguer votre service cloud avant de le déployer. L’émulateur gère le cycle de vie de vos instances de rôles et fournit l’accès aux ressources simulés, tels que le stockage local. Lorsque vous déboguez ou exécutez votre service à partir de Visual Studio, il démarre automatiquement l’émulateur comme une application d’arrière-plan et puis déploie votre service à l’émulateur. Vous pouvez utiliser l’émulateur pour consulter votre service, lorsqu’il s’exécute dans l’environnement local. Vous pouvez exécuter la version complète ou la version expresse de l’émulateur. (En commençant par Azure 2.3, la version expresse de l’émulateur est la valeur par défaut). Reportez-vous [à l’aide de l’émulateur Express pour exécuter et déboguer un Service en nuage localement](https://msdn.microsoft.com/library/dn339018.aspx).

### <a name="to-debug-your-cloud-service-on-your-local-computer"></a>Pour déboguer votre service cloud sur votre ordinateur local

1. Dans la barre de menus, cliquez sur **Déboguer**, **Démarrer le débogage** pour exécuter le projet de service cloud Azure. Comme alternative, vous pouvez appuyer sur F5. Vous verrez un message de démarrage de l’émulateur de calcul. Lorsque l’émulateur démarre, l’icône de la barre d’état système le confirme.

    ![Émulateur Azure dans la barre d’état système](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC783828.png)

1. Afficher l’interface utilisateur de l’émulateur de calcul en ouvrant le menu contextuel de l’icône Azure dans la zone de notification et sélectionnez **Afficher l’interface utilisateur émulateur de calcul**.

    Le volet de gauche de l’interface utilisateur affiche les services qui sont actuellement déployés à l’émulateur de calcul et les instances de rôles que chaque service est en cours d’exécution. Vous pouvez choisir les services ou les rôles pour afficher des informations de diagnostic, de consignation et de cycle de vie dans le volet droit. Si vous placez le focus dans la marge supérieure d’une fenêtre incluse, il se développe pour remplir le volet de droite.

1. Exécutez l’application en sélectionnant des commandes dans le menu **Déboguer** et en définissant des points d’arrêt dans votre code. Pendant que vous parcourez l’application dans le débogueur, les volets sont mis à jour avec l’état actuel de l’application. Lorsque vous arrêtez le débogage, le déploiement de l’application est supprimé. Si votre application inclut un rôle web et que vous avez défini la propriété action de démarrage pour démarrer le navigateur web, Visual Studio démarre votre application web dans le navigateur. Si vous modifiez le nombre d’instances d’un rôle dans la configuration du service, vous devez arrêter votre service cloud et relancez le débogage afin que vous puissiez déboguer ces nouvelles instances du rôle.

    **Remarque :** Lorsque vous arrêtez l’exécution ou le débogage de votre service, l’émulateur de calcul locale et l’émulateur de stockage ne sont pas arrêtés. Vous devez arrêter les explicitement à partir de la zone de notification.


## <a name="debug-a-cloud-service-in-azure"></a>Débogage d’un service cloud dans Azure

Pour déboguer un service en nuage à partir d’un ordinateur distant, vous devez activer cette fonctionnalité explicitement lorsque vous déployez votre service cloud par services (msvsmon.exe, par exemple) sont installés sur les ordinateurs virtuels qui s’exécutent vos instances de rôles. Si vous n’avez pas activer le débogage distant lorsque vous avez publié le service, vous devez republier le service avec le débogage distant est activée.

Si vous activez le débogage à distance pour un service en nuage, il ne présenter des performances dégradées ou payer des frais supplémentaires. Vous n’utilisez pas le débogage distant sur un service de production, car les clients qui utilisent le service peuvent être compromises.

>[AZURE.NOTE] Lorsque vous publiez un service en nuage à partir de Visual Studio, vous pouvez activer **IntelliTrace** pour tous les rôles dans ce service qui ciblent.NET Framework 4 ou le 4.5 de.NET Framework. À l’aide **d’IntelliTrace**, vous pouvez examiner les événements survenus dans une instance de rôle dans le passé et reproduire le contexte à partir de ce moment. Consultez [débogage d’un service cloud publié avec IntelliTrace et Visual Studio](http://go.microsoft.com/fwlink/?LinkID=623016) et [à l’aide de IntelliTrace](https://msdn.microsoft.com/library/dd264915.aspx).

### <a name="to-enable-remote-debugging-for-a-cloud-service"></a>Pour activer le débogage à distance pour un service en nuage

1. Ouvrir le menu contextuel du projet Azure et sélectionnez **Publier**.

1. Sélectionnez l’environnement **mis en œuvre** et la configuration de **débogage** .

    Ce n'est qu’une indication. Vous pouvez choisir d’exécuter vos environnements de test dans un environnement de Production. Toutefois, risquerait d’affecter les utilisateurs si vous activez le débogage distant sur l’environnement de Production. Vous pouvez choisir la configuration Release, mais la configuration de débogage qui rend le débogage plus facile.

    ![Choisissez la configuration Debug](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746717.gif)

1. Suivez la procédure habituelle, mais activez la case à cocher **Activer le débogueur distant pour tous les rôles** dans l’onglet **Paramètres avancés** .

    ![Configuration de débogage](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746718.gif)

### <a name="to-attach-the-debugger-to-a-cloud-service-in-azure"></a>Pour attacher le débogueur à un service en nuage dans Azure

1. Dans l’Explorateur de serveurs, développez le nœud de votre service cloud.

1. Ouvrir le menu contextuel pour le rôle ou l’instance de rôle auquel vous souhaitez joindre et sélectionnez **Attacher le débogueur**.

    Si vous déboguez un rôle, le débogueur de Visual Studio s’attache à chaque instance de ce rôle. Le débogueur s’arrête sur un point d’arrêt pour la première instance de rôle qui exécute cette ligne de code et la remplit les conditions de ce point d’arrêt. Si vous déboguez une instance, le débogueur attache uniquement cette instance et de sauts sur un point d’arrêt que lorsque cette instance spécifique s’exécute cette ligne de code et qu’il remplit les conditions de point d’arrêt.

    ![Attacher le débogueur](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746719.gif)

1. Une fois que le débogueur s’attache à une instance, débogage comme d’habitude. Le débogueur s’attache automatiquement au processus hôte approprié pour votre rôle. Selon le rôle, le débogueur s’attache à w3wp.exe, WaWorkerHost.exe ou WaIISHost.exe. Pour vérifier le processus auquel le débogueur est attaché, développez le nœud de l’instance dans l’Explorateur de serveurs. Pour plus d’informations sur les processus d’Azure, consultez [Architecture de rôle Azure](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx) .

    ![Sélectionnez la boîte de dialogue de type de code](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)

1. Pour identifier les processus auquel le débogueur est attaché, ouvrez la boîte de dialogue processus, dans la barre de menus, choisissez débogage, fenêtres, processus. (Clavier : Ctrl + Alt + Z) Pour détacher un processus spécifique, ouvrez le menu contextuel et sélectionnez **Détacher le processus**. Ou, recherchez le nœud d’instance dans l’Explorateur de serveurs, recherchez le processus, ouvrir le menu contextuel et puis sélectionnez **Détacher le processus**.

    ![Déboguer des processus](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC690787.gif)

>[AZURE.WARNING] Éviter de longs arrête à des points d’arrêt à distance de débogage. Azure traite un processus qui est arrêté pendant plus de quelques minutes ne répond plus et interrompt l’envoi de trafic à cette instance. Si vous arrêtez pendant trop longtemps, msvsmon.exe se détache du processus.

Pour détacher le débogueur à partir de tous les processus de votre instance ou le rôle, ouvrez le menu contextuel pour le rôle ou l’instance de débogage et sélectionnez **Détacher le débogueur**.

## <a name="limitations-of-remote-debugging-in-azure"></a>Limitations du débogage à distance dans Azure

À partir de 2.3 du SDK Azure, le débogage à distance présente les limitations suivantes.

- Avec le débogage distant activé, vous ne pouvez pas publier un service en nuage dans lequel n’importe quel rôle possède plus de 25 instances.

- Le débogueur utilise les ports 30400 à 30424, 31400 à 31424 et 32400 à 32424. Si vous essayez d’utiliser un de ces ports, vous ne pourrez pas publier votre service, et un des messages d’erreur suivants s’affiche dans le journal d’activité pour Azure : 

    - Erreur lors de la validation du fichier .cscfg dans le fichier .csdef. 
    La plage de port réservé 'plage' pour point de terminaison Microsoft.WindowsAzure.Plugins.RemoteDebugger.Connector du rôle 'rôle' chevauche un port déjà définies ou de la plage.
    - Échoué de l’allocation. Veuillez réessayer plus tard, essayez de réduire la taille de mémoire virtuelle ou le nombre d’instances de rôle ou essayez de déployer dans une autre région.


## <a name="debugging-azure-virtual-machines"></a>Débogage des machines virtuelles Azure

Vous pouvez déboguer des programmes qui s’exécutent sur des machines virtuelles Azure à l’aide de l’Explorateur de serveurs dans Visual Studio. Lorsque vous activez le débogage distant sur un ordinateur virtuel Azure, Azure installe l’extension de débogage distante sur l’ordinateur virtuel. Ensuite, vous pouvez attacher des processus sur l’ordinateur virtuel et de débogage comme vous le feriez normalement.

>[AZURE.NOTE] Une machine virtuelle créée par l’intermédiaire de la pile de gestionnaire de ressources Azure peuvent être déboguée à distance à l’aide de Cloud Explorer dans Visual Studio 2015. Pour plus d’informations, consultez [Gestion des ressources avec le Cloud Explorer Azure](http://go.microsoft.com/fwlink/?LinkId=623031).

### <a name="to-debug-an-azure-virtual-machine"></a>Pour déboguer un ordinateur virtuel Azure

1. Dans l’Explorateur de serveurs, développez le nœud de Machines virtuelles et sélectionnez le nœud de l’ordinateur virtuel que vous souhaitez déboguer.

1. Ouvrir le menu contextuel, puis sélectionnez **Activer le débogage**. Lorsque le système vous demande si vous êtes certain que si vous souhaitez activer le débogage sur l’ordinateur virtuel, cliquez sur **Oui**.

    Azure installe l’extension de débogage distante sur l’ordinateur virtuel pour activer le débogage.

    ![Commande de débogage activer la machine virtuelle](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746720.png)

    ![Journal d’activité Azure](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746721.png)

1. L’extension de débogage à distance après l’installation, ouvrez le menu contextuel de la machine virtuelle et sélectionnez **Attacher le débogueur...**

    Azure Obtient une liste des processus sur l’ordinateur virtuel et les affiche dans l’attacher à la boîte de dialogue processus.

    ![Joindre la commande du débogueur](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746722.png)

1. Dans la boîte de dialogue **Attacher au processus** , sélectionnez **Sélectionner** pour limiter la liste des résultats pour afficher uniquement les types de code que vous souhaitez déboguer. Vous pouvez déboguer le code de 32 ou 64 bits managé, le code natif ou les deux.

    ![Sélectionnez la boîte de dialogue de type de code](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)

1. Sélectionnez les processus que vous souhaitez déboguer sur l’ordinateur virtuel et cliquez sur **attacher**. Par exemple, vous pouvez choisir le processus w3wp.exe si vous souhaitez déboguer une application web sur l’ordinateur virtuel. Pour plus d’informations, consultez [débogage un ou plusieurs processus dans Visual Studio](https://msdn.microsoft.com/library/jj919165.aspx) et [l’Architecture de rôle Azure](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx) .

## <a name="create-a-web-project-and-a-virtual-machine-for-debugging"></a>Créer un projet web et une machine virtuelle pour le débogage

Avant de publier votre projet Azure, vous trouverez utile de la tester dans un environnement de contenus qui prend en charge le débogage et le test des scénarios, et où vous pouvez installer les tests et la surveillance des programmes. Une façon de procéder est à déboguer à distance de votre application sur une machine virtuelle.

Les projets Visual Studio ASP.NET offrent une option pour créer un ordinateur virtuel pratique que vous pouvez utiliser pour le test de l’application. L’ordinateur virtuel inclut ont fréquemment besoin de points de terminaison comme PowerShell, Bureau à distance et WebDeploy.

### <a name="to-create-a-web-project-and-a-virtual-machine-for-debugging"></a>Pour créer un projet web et une machine virtuelle pour le débogage

1. Dans Visual Studio, créez une Application Web ASP.NET.

1. Dans la boîte de dialogue Nouveau projet ASP.NET, dans la section Azure, choisissez **ordinateur virtuel** dans la zone de liste déroulante. Laissez la case à cocher **créer des ressources à distance** activée. Cliquez sur **OK** pour continuer.

    La boîte de dialogue **créer des VM sur Azure** s’affiche.


    ![Créer la boîte de dialogue de projet web ASP.NET](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746723.png)

    **Remarque :** Vous devrez vous connecter à votre compte Azure si vous n’êtes pas déjà connecté.

1. Sélectionnez les différents paramètres de l’ordinateur virtuel, puis sélectionnez **OK**. Pour plus d’informations, reportez-vous à la section [ordinateurs virtuels]( http://go.microsoft.com/fwlink/?LinkId=623033) .

    Le nom que vous entrez pour le nom DNS sera le nom de l’ordinateur virtuel. 

    ![Créer un ordinateur virtuel dans la boîte de dialogue Azure](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746724.png)

    Azure crée l’ordinateur virtuel, puis les dispositions et configure les points de terminaison, tel que le Bureau à distance et Web Deploy



1. Une fois l’ordinateur virtuel entièrement configuré, sélectionnez le nœud de la machine virtuelle dans l’Explorateur de serveurs.

1. Ouvrir le menu contextuel, puis sélectionnez **Activer le débogage**. Lorsque le système vous demande si vous êtes certain que si vous souhaitez activer le débogage sur l’ordinateur virtuel, cliquez sur **Oui**. 

    Azure installe l’extension de débogage distante sur l’ordinateur virtuel pour activer le débogage.

    ![Commande de débogage activer la machine virtuelle](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746720.png)

    ![Journal d’activité Azure](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746721.png)

1. Publier votre projet comme décrit dans [Comment : déployer un projet à l’aide d’un seul clic publication Web dans Visual Studio](https://msdn.microsoft.com/library/dd465337.aspx). Étant donné que vous souhaitez déboguer sur l’ordinateur virtuel, dans la page **paramètres** de l’Assistant **Publication sur le Web** , sélectionnez **Déboguer** la configuration. Cela permet de garantir que les symboles de code sont disponibles pendant le débogage.

    ![Paramètres de publication](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718349.png)

1. Dans les **Options de publication du fichier**, sélectionnez **Supprimer les fichiers supplémentaires à la destination** si le projet a déjà été déployé à un moment antérieur.

1. Une fois le projet publie, dans le menu contextuel de la machine virtuelle dans l’Explorateur de serveurs, sélectionnez **Attacher le débogueur...**

    Azure Obtient une liste des processus sur l’ordinateur virtuel et les affiche dans l’attacher à la boîte de dialogue processus.

    ![Joindre la commande du débogueur](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746722.png)

1. Dans la boîte de dialogue **Attacher au processus** , sélectionnez **Sélectionner** pour limiter la liste des résultats pour afficher uniquement les types de code que vous souhaitez déboguer. Vous pouvez déboguer le code de 32 ou 64 bits managé, le code natif ou les deux.

    ![Sélectionnez la boîte de dialogue de type de code](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)

1. Sélectionnez les processus que vous souhaitez déboguer sur l’ordinateur virtuel et cliquez sur **attacher**. Par exemple, vous pouvez choisir le processus w3wp.exe si vous souhaitez déboguer une application web sur l’ordinateur virtuel. Pour plus d’informations, consultez [débogage un ou plusieurs processus dans Visual Studio](https://msdn.microsoft.com/library/jj919165.aspx) .

## <a name="next-steps"></a>Étapes suivantes

- Utilisez **Intellitrace** pour collecter un journal d’appels et les événements à partir d’un serveur de publication. Consultez [débogage d’un Service en nuage publié avec IntelliTrace et Visual Studio](http://go.microsoft.com/fwlink/?LinkID=623016).
- **Diagnostics d’Azure** permet d’enregistrer des informations détaillées à partir du code s’exécutant dans des rôles, si les rôles sont en cours d’exécution dans l’environnement de développement ou dans Azure. Consultez [collecte l’enregistrement des données avec Azure Diagnostics](http://go.microsoft.com/fwlink/p/?LinkId=400450).
