<properties
    pageTitle="Tomcat sur une machine virtuelle | Microsoft Azure"
    description="Ce didacticiel utilise les ressources créées avec le modèle de déploiement classique et montre comment créer un ordinateur virtuel Windows et configurez-le pour exécuter le serveur d’application Apache Tomcat."
    services="virtual-machines-windows"
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""
    tags="azure-service-management" />

<tags
    ms.service="virtual-machines-windows"
    ms.workload="web"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="how-to-run-a-java-application-server-on-a-virtual-machine-created-with-the-classic-deployment-model"></a>Comment faire pour exécuter un serveur d’applications Java sur une machine virtuelle créée avec le modèle classique de déploiement

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Avec Azure, vous pouvez utiliser un ordinateur virtuel pour fournir des fonctions de serveur. Par exemple, une machine virtuelle en cours d’exécution sur Azure peut être configurée pour héberger un serveur d’applications Java, tels que Apache Tomcat. Après avoir terminé ce guide, vous devez comprendre comment créer un ordinateur virtuel en cours d’exécution sur Azure et configurez-le pour exécuter un serveur d’applications Java.

Vous allez apprendre :

* Comment créer un ordinateur virtuel qui a un Kit de développement Java (JDK) déjà installé.
* Comment se connecter à distance à votre ordinateur virtuel.
* Comment installer un serveur d’applications Java sur votre machine virtuelle.
* Comment créer un point de terminaison de votre machine virtuelle.
* Comment ouvrir un port dans le pare-feu pour votre serveur d’application.

Pour les besoins de ce didacticiel, un serveur d’application Apache Tomcat va être installé sur une machine virtuelle. Une installation de Tomcat semblable à la suivante provoque l’installation terminée.

![Machine virtuelle s’exécutant Apache Tomcat][virtual_machine_tomcat]

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="to-create-a-virtual-machine"></a>Pour créer un ordinateur virtuel

1. Connectez-vous à l' [Azure portal classique](https://manage.windowsazure.com).
2. Cliquez sur **Nouveau**et cliquez sur **Calculer**, cliquez sur **ordinateur virtuel**, puis cliquez sur **à partir de la galerie**.
3. Dans la boîte de dialogue **Sélectionner des images de machine virtuelle** , sélectionnez **JDK 7 Windows Server 2012**.
Notez que le **JDK 6 Windows Server 2012** est disponible si vous avez des applications anciennes qui ne sont pas prêtes à s’exécuter dans le JDK 7.
4. Cliquez sur **suivant**.
5. Dans la boîte de dialogue de **configuration de machine virtuelle** :
    1. Spécifiez un nom pour l’ordinateur virtuel.
    2. Spécifiez la taille à utiliser pour la machine virtuelle.
    3. Dans le champ **Nom d’utilisateur** , entrez un nom pour l’administrateur. N’oubliez pas ce nom et le mot de passe que vous entrez ensuite, vous allez l’utiliser pour vous connecter à distance à l’ordinateur virtuel.
    4. Entrez un mot de passe dans le champ **nouveau mot de passe** , et entrez-le dans le champ **Confirmer** . C’est le mot de passe du compte administrateur.
    5. Cliquez sur **suivant**.
6. Dans la boîte de dialogue **configuration de l’ordinateur virtuel** suivante :
    1. Pour le **service en nuage**, utilisez la valeur par défaut de **créer un nouveau service en nuage**.
    2. La valeur pour le **nom DNS du service nuage** doit être unique au sein de cloudapp.net. Si nécessaire, modifiez cette valeur afin qu’Azure indique qu’il est unique.
    2. Permet de spécifier une région, une affinité du groupe ou réseau virtuel. Pour les besoins de ce didacticiel, spécifier une région, par exemple **US-ouest**.
    2. Pour le **Compte de stockage**, sélectionnez **utiliser un compte de stockage généré automatiquement**.
    3. Pour **Définir de disponibilité**, sélectionnez **(aucun)**.
    4. Cliquez sur **suivant**.
7. Dans la dernière boîte de dialogue de **configuration de machine virtuelle** :
    1. Accepter les entrées de point de terminaison par défaut.
    2. Cliquez sur **terminé**.

## <a name="to-remotely-sign-in-to-your-virtual-machine"></a>Pour vous connecter à distance à votre machine virtuelle

1. Ouvrez une session sur [Azure portal classique](https://manage.windowsazure.com).
2. Cliquez sur des **machines virtuelles**.
3. Cliquez sur le nom de l’ordinateur virtuel que vous souhaitez vous connecter à.
4. Après le démarrage de la machine virtuelle, un menu déroulant au bas de la page autorise les connexions.
5. Cliquez sur **se connecter**.
6. Répondez aux invites comme nécessaire pour se connecter à l’ordinateur virtuel. Cela entraîne l’enregistrement ou l’ouverture du fichier .rdp qui contient les détails de la connexion. Vous devrez peut-être copier l’url : port dans la dernière partie de la première ligne du fichier .rdp et le coller dans une application de connexion à distance.

## <a name="to-install-a-java-application-server-on-your-virtual-machine"></a>Pour installer un serveur d’applications Java sur votre machine virtuelle

Vous pouvez copier un serveur d’applications Java de votre ordinateur virtuel, ou vous pouvez installer un serveur d’applications Java via un programme d’installation.

Pour les besoins de ce didacticiel, Tomcat sera installé.

1. Lorsque vous êtes connecté à votre ordinateur virtuel, ouvrez une session de navigateur à [Apache Tomcat](http://tomcat.apache.org/download-70.cgi).
2. Double-cliquez sur le lien pour le **Service d’installation Windows de 32 bits/64 bits**. À l’aide de cette technique, Tomcat sera installé comme un service Windows.
3. Lorsque vous y êtes invité, choisissez d’exécuter le programme d’installation.
4. Dans l’Assistant **Installation de Tomcat Apache** , suivez les instructions d’installation de Tomcat. Pour les besoins de ce didacticiel, acceptez les valeurs par défaut est parfait. Lorsque vous atteignez la boîte de dialogue **fin de l’Assistant de configuration Apache Tomcat** , vous pouvez éventuellement vérifier **Exécuter Apache Tomcat** pour que Tomcat Démarrer maintenant. Cliquez sur **Terminer** pour terminer le processus d’installation de Tomcat.

## <a name="to-start-tomcat"></a>Pour démarrer Tomcat
Si vous n’avez pas choisi d’exécuter Tomcat dans la boîte de dialogue **fin de l’Assistant de configuration Apache Tomcat** , démarrez-le en ouvrant une invite de commande sur votre machine virtuelle et **démarre le Tomcat7**en cours d’exécution.

Vous devez maintenant voir Tomcat si vous exécutez l’Explorateur de la machine virtuelle et que vous ouvrez <http://localhost : 8080>.

Pour visualiser les Tomcat en cours d’exécution à partir d’ordinateurs externes, vous devez créer un point de terminaison et d’ouvrir un port.

## <a name="to-create-an-endpoint-for-your-virtual-machine"></a>Pour créer un point de terminaison de votre machine virtuelle
1. Connectez-vous à l' [Azure portal classique](https://manage.windowsazure.com).
2. Cliquez sur des **machines virtuelles**.
3. Cliquez sur le nom de l’ordinateur virtuel qui exécute le serveur d’applications Java.
4. Cliquez sur des **points de terminaison**.
5. Cliquez sur **Ajouter**.
6. Dans la boîte de dialogue **Ajout de point de terminaison** , vérifiez le **point de terminaison ajouter autonome** est sélectionnée, puis cliquez sur **suivant**.
7. Dans la boîte de dialogue **Détails du nouveau point de terminaison** :
    1. Spécifiez un nom pour le point de terminaison ; par exemple, **HttpIn**.
    2. Indiquez **TCP** pour le protocole.
    3. Spécifiez **80** pour le port public.
    4. Spécifiez **8080** pour le port privé.
    5. Cliquez sur le bouton **Terminer** pour fermer la boîte de dialogue. Votre point de terminaison va être créé.

## <a name="to-open-a-port-in-the-firewall-for-your-virtual-machine"></a>Pour ouvrir un port dans le pare-feu de votre machine virtuelle
1. Connectez-vous à votre machine virtuelle.
2. Cliquez sur **Démarrer**.
3. Cliquez sur **Panneau de configuration**.
4. Cliquez sur **système et sécurité**, cliquez sur **Pare-feu Windows**, puis cliquez sur **Paramètres avancés**.
5. Cliquez sur **Règles de trafic entrant**, puis cliquez sur **Nouvelle règle**.
 ![Nouvelle règle de trafic entrant][NewIBRule]
6. Pour le **Type de règle**, sélectionnez le **Port**, puis cliquez sur **suivant**.
 ![Nouveau port de règle de trafic entrant][NewRulePort]
7. Dans l’écran **Ports et protocole** , sélectionnez **TCP**, spécifiez **8080** comme **port local spécifique**et puis cliquez sur **suivant**.
 ![Nouvelle règle de trafic entrant][NewRuleProtocol]
8. Dans l’écran **Action** , sélectionnez **Autoriser la connexion**, puis cliquez sur **suivant**.
 ![Nouvelle action de règle de trafic entrant][NewRuleAction]
9. Dans l’écran **profil** , vérifiez que **domaine** **privé**et **Public** sont sélectionnés, puis cliquez sur **suivant**.
 ![Nouveau profil de règle de trafic entrant][NewRuleProfile]
10. Dans l’écran **nom** , spécifiez un nom pour la règle, comme **HttpIn** (le nom de la règle n’est pas nécessaire pour faire correspondre le nom du point de terminaison, de toutefois) et puis cliquez sur **Terminer**.  
 ![Nouveau nom de règle de trafic entrant][NewRuleName]

À ce stade, votre site Web de Tomcat doit être visible à partir d’un navigateur externe en utilisant une URL du formulaire * *http://*votre\_DNS\_nom de*. cloudapp.net**où ** *votre\_DNS\_nom*** est le nom DNS que vous avez spécifié lorsque vous avez créé l’ordinateur virtuel.

## <a name="application-lifecycle-considerations"></a>Considérations sur l’application du cycle de vie
* Vous pouvez archiver dans votre propre application web (WAR) et l’ajouter dans le dossier **d’applications Web** . Par exemple, créer un projet web dynamique de base Java Service Page (JSP) et l’exporter sous la forme d’un fichier WAR, copier de la guerre dans le dossier **d’applications Web** Apache Tomcat sur l’ordinateur virtuel, puis l’exécuter dans un navigateur.
* Par défaut lorsque le service Tomcat est installé, il est configuré pour démarrer manuellement. Vous pouvez basculer qu’il démarre automatiquement à l’aide du composant logiciel enfichable Services. Démarrez le composant logiciel enfichable Services en cliquant sur **Démarrer**, **Outils d’administration**, puis **Services**. Double-cliquez sur le service **Apache Tomcat** et définissez le **type de démarrage** sur **automatique**.

    ![Définition d’un service à démarrage automatique][service_automatic_startup]

    L’avantage de disposer automatiquement de démarrer de Tomcat est qu’il va démarrer si la machine virtuelle est redémarrée (par exemple, après l’installent de mises à jour logicielles requièrent un redémarrage).

## <a name="next-steps"></a>Étapes suivantes
Obtenir des informations sur les autres services (tels que le stockage Azure, un bus de service et de la base de données SQL) que vous voudrez inclure avec vos applications Java en affichant les informations disponibles dans le [Centre de développement Java](https://azure.microsoft.com/develop/java/).

[virtual_machine_tomcat]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/WA_VirtualMachineRunningApacheTomcat.png

[service_automatic_startup]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/WA_TomcatServiceAutomaticStart.png









[NewIBRule]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewInboundRule.png
[NewRulePort]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRulePort.png
[NewRuleProtocol]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRuleProtocol.png
[NewRuleAction]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRuleAction.png
[NewRuleName]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRuleName.png
[NewRuleProfile]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRuleProfile.png
