<properties
    pageTitle="Configurez Apache Tomcat sur un ordinateur virtuel de Linux | Microsoft Azure"
    description="Découvrez comment configurer Apache Tomcat7 à l’aide d’une machine virtuelle Azure (VM) fonctionnant sous Linux."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="NingKuang"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/15/2015"
    ms.author="ningk"/>

#<a name="how-to-set-up-tomcat7-on-a-linux-virtual-machine-with-microsoft-azure"></a>Comment faire pour configurer le Tomcat7 sur un ordinateur virtuel de Linux avec Microsoft Azure

Apache Tomcat (ou simplement Tomcat, anciennement également Jakarta Tomcat) est un serveur web open source et un conteneur de servlet développé par l’Apache Software Foundation (ASF). Tomcat implémente le Servlet Java et les spécifications de JavaServer Pages (JSP) de Sun Microsystems et offre un environnement de serveur web HTTP de Java pur dans lequel exécuter le code de Java. Dans la configuration la plus simple, Tomcat s’exécute dans un processus de système d’exploitation. Ce processus s’exécute à une machine virtuelle de Java (JVM). Toutes les demandes HTTP à partir d’un navigateur pour Tomcat sont traité comme un thread séparé dans le processus de Tomcat.  

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Dans ce guide, vous installerez tomcat7 sur une image de Linux et déployez-le dans Microsoft Azure.  

Vous allez apprendre :  

-   Comment créer un ordinateur virtuel dans Azure.
-   Comment faire pour préparer l’ordinateur virtuel tomcat7.
-   L’installation de tomcat7.

Il est supposé que le lecteur possède déjà un abonnement Azure.  Si ce n’est pas le cas, vous pouvez vous inscrire pour une évaluation gratuite sur [http://azure.microsoft.com](https://azure.microsoft.com/). Si vous avez un abonnement MSDN, consultez [tarification spéciale de Microsoft Azure : MSDN et MPN Bizspark avantages](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/?c=14-39). Pour en savoir plus sur Azure, consultez [Nouveautés Azure ?](https://azure.microsoft.com/overview/what-is-azure/).

Cette rubrique suppose que vous avez base de connaissance de tomcat et Linux.  

##<a name="phase-1-create-an-image"></a>Phase 1 : Créer une image
Dans cette phase, vous allez créer un ordinateur virtuel à l’aide d’une image de Linux dans Azure.  

###<a name="step-1-generate-an-ssh-authentication-key"></a>Étape 1 : Générer une clé d’authentification SSH
SSH est un outil important pour les administrateurs système. Toutefois, la configuration de la sécurité d’accès basée sur un mot de passe déterminé par l’homme n’est pas recommandé. Des utilisateurs malveillants peuvent pénétrer votre système basé sur un nom d’utilisateur et un mot de passe faible.

La bonne nouvelle est qu’il existe un moyen de laisser accès distant ouvert et avez ne pas à vous soucier des mots de passe. La méthode se compose de l’authentification avec la cryptographie asymétrique. Clé privée de l’utilisateur est celui qui accorde l’authentification. Vous pouvez même verrouiller le compte d’utilisateur pour interdire complètement de l’authentification du mot de passe.

Un autre avantage de cette méthode est que vous n’avez pas besoin de mots de passe différents pour vous connecter à des serveurs différents. Vous pouvez vous authentifier à l’aide de la clé privée personnelle sur tous les serveurs, qui vous évite d’avoir à mémoriser plusieurs mots de passe.

Il est également possible de se connecter avec nécessitant un mot de passe avec cette méthode.

Procédez comme suit pour générer la clé d’authentification SSH.

1.  Téléchargez et installez puttygen à partir de l’emplacement suivant : [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2.  Exécutez PUTTYGEN. EXE.
3.  Cliquez sur **Générer** pour générer les clés. Dans le processus, vous pouvez augmenter aléatoire en déplaçant la souris sur la zone vide située dans la fenêtre.  
![][1]
4.  Après le processus de génération, Puttygen.exe affiche votre clé générée. Par exemple :  
![][2]
5.  Sélectionnez et copiez la clé publique de la **clé** et l’enregistrer dans un fichier nommé publicKey.pem. Ne cliquez pas sur **Enregistrer la clé publique**, étant donné que le format de fichier de la clé publique enregistrée est différente de la clé publique que nous.
6.  Cliquez sur **Enregistrer la clé privée** et l’enregistrer dans un fichier nommé privateKey.ppk.

###<a name="step-2-create-the-image-in-the-azure-portal"></a>Étape 2 : Créer l’image dans le portail Azure.
Dans le [portail Azure](https://portal.azure.com/), cliquez sur **Nouveau** dans la barre des tâches pour créer une image, choisissez l’image de Linux en fonction de vos besoins. L’exemple suivant utilise l’image de 14.04 d’Ubuntu.
![][3]

Pour le **Nom de l’hôte de** spécifier le nom de l’URL que les clients Internet et vous utiliserez pour accéder à cet ordinateur virtuel. Définir la dernière partie du nom DNS, par exemple tomcatdemo, et Azure va générer l’URL en tant que tomcatdemo.cloudapp.net.  

Pour **Clé d’authentification SSH**, copiez la valeur de clé à partir du fichier **publicKey.pem** , qui contient la clé publique générée par puttygen.  
![][4]

Configurer d’autres paramètres en fonction des besoins, puis cliquez sur Créer.  

##<a name="phase-2-prepare-your-virtual-machine-for-tomcat7"></a>Phase 2 : Préparer votre machine virtuelle pour Tomcat7
Dans cette phase, vous configurez un point final pour le trafic de tomcat, puis connectez-vous à votre nouvelle machine virtuelle.
###<a name="step-1-open-the-http-port-to-allow-web-access"></a>Étape 1 : Ouvrir le port HTTP pour autoriser l’accès web
Points de terminaison dans Azure se composent d’un protocole (TCP ou UDP), avec un port public et privé. Le port privé est le port que le service est à l’écoute sur l’ordinateur virtuel. Le port public est le port que le service en nuage Azure est à l’écoute sur l’extérieur pour le trafic entrant, basée sur Internet.  

Le port TCP 8080 est le port par défaut sur lequel écoute de tomcat. Ouvrir ce port avec un point de terminaison Azure permettra de vous et autre clients Internet à accéder aux pages de tomcat.  

1.  Dans le portail Azure, cliquez sur **Parcourir** -> **ordinateur virtuel**, puis cliquez sur l’ordinateur virtuel que vous avez créé.  
![][5]
2.  Pour ajouter un point de terminaison à votre machine virtuelle, cliquez sur la **points de terminaison** de zone.
![][6]
3.  Cliquez sur **Ajouter**.  
    1.  Pour que le **point de terminaison**, tapez un nom pour le point de terminaison dans le point de terminaison et puis entrez 80 dans **Port Public**.  

        Si vous la définissez à 80, n’est pas nécessaire d’inclure le numéro de port dans l’URL qui vous permet d’accéder aux tomcat. Par exemple, http://tomcatdemo.cloudapp.net.    

        Si vous la définissez à une autre valeur, par exemple 81, vous devez ajouter le numéro de port à l’URL pour accéder à tomcat. Par exemple, http://tomcatdemo.cloudapp.net:81 /.
    2.  Dans la zone Port de privé, tapez 8080. Par défaut, tomcat est à l’écoute sur le port TCP 8080. Si vous avez modifié la valeur par défaut écoute le port de tomcat, vous devez mettre à jour Port privé pour être que le même que le service tomcat port d’écoute.  
    ![][7]

4.  Cliquez sur **OK** pour ajouter le point de terminaison à votre machine virtuelle.



###<a name="step-2-connect-to-the-image-you-created"></a>Étape 2 : Se connecter à l’image que vous avez créé.
Vous pouvez choisir n’importe quel outil SSH pour se connecter à votre machine virtuelle. Dans cet exemple, nous utilisons Putty.  

Tout d’abord, obtenir le nom DNS de votre ordinateur virtuel à partir du portail Azure. **Cliquez sur Parcourir** -> **machines virtuelles** -> le nom de votre machine virtuelle -> **Propriétés**et recherchez dans le champ de **Nom de domaine** de la mosaïque de **Propriétés** .  

Obtenir le numéro de port pour identifier les connexions SSH à partir du champ **SSH** . Voici un exemple.  
![][8]

Télécharger Putty à partir [d’ici](http://www.putty.org/) .  

Après le téléchargement, cliquez sur le fichier exécutable PUTTY. EXE. Configurer les options de base avec le nom d’hôte et le port numéro obtenu à partir des propriétés de votre machine virtuelle. Voici un exemple :  
![][9]

Dans le volet gauche, cliquez sur **la connexion** -> **SSH** -> **faisant autorité** , puis cliquez sur **Parcourir** pour spécifier l’emplacement du fichier **privateKey.ppk** , qui contient la clé privée générée par puttygen en Phase 1 : créer une Image. Voici un exemple :  
![][10]

Cliquez sur **Ouvrir**. Vous pouvez être averti par une boîte de message. Si vous avez configuré le nom DNS et numéro de port correctement, cliquez sur **Oui**.
![][11]  


Vous devez voir les éléments suivants :  
![][12]

Entrez le nom d’utilisateur spécifié lorsque vous avez créé l’ordinateur virtuel à la Phase 1 : créer une Image. Vous verrez quelque chose comme suit :  
![][13]





##<a name="phase-3-install-software"></a>Pour la phase 3 : Installer le logiciel
Dans cette phase, vous installez l’environnement d’exécution Java, tomcat et d’autres composants de tomcat.  

###<a name="java-runtime-environment"></a>Environnement d’exécution Java
Tomcat est écrit en Java. Il existe deux types de Kits de développement Java (kits JDK) (OpenJDK et Oracle JDK), et vous pouvez choisir celui qui que vous intéresse.  

>AZURE. Remarque : Les deux kits JDK apparaître presque le même code pour les classes de l’API Java, mais le code de la machine virtuelle est en réalité différent. Lorsqu’il s’agit de bibliothèques, OpenJDK a tendance à utiliser des bibliothèques ouvertes tandis que Oracle a tendance à utiliser ceux qui sont fermés. Mais Oracle JDK a plusieurs classes et certains bogues de fixe et JDK d’Oracle est plus stable que OpenJDK.

Les commandes suivantes téléchargement les kits JDK différents.  

Ouvrir-jdk   

    sudo apt-get update  
    sudo apt-get install openjdk-7-jre  

Oracle-jdk  

-   Pour télécharger le JDK à partir du site Web d’Oracle :  

        wget --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u5-b13/jdk-8u5-linux-x64.tar.gz  

-   Pour créer un répertoire contenant les fichiers JDK :  

        sudo mkdir /usr/lib/jvm  

-   Pour extraire les fichiers JDK dans le répertoire/usr/lib/jvm / :  

        sudo tar -zxf jdk-8u5-linux-x64.tar.gz  -C /usr/lib/jvm/  

-   Pour configurer Oracle JDK JVM par défaut :  

        sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/jdk1.8.0_05/bin/java 100  
        sudo update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/jdk1.8.0_05/bin/javac 100  

####<a name="test"></a>Test :
Vous pouvez utiliser une commande comme suit pour vérifier si l’environnement d’exécution Java est installé correctement :  

    java -version  

Si vous avez installé open-jdk, vous devriez voir un message semblable à celui-ci :![][14]

Si vous avez installé oracle-jdk, vous devriez voir un message semblable à celui-ci :![][15]

###<a name="tomcat7"></a>Tomcat7
À l’aide de la commande suivante pour installer le tomcat7 :  

    sudo apt-get install tomcat7  

Si vous n’utilisez pas tomcat7, utilisez la variation appropriée de cette commande.  

####<a name="test"></a>Test :

Pour vérifier que tomcat7 est correctement installé, recherchez le nom DNS de votre serveur tomcat (http://tomcatexample.cloudapp.net/ est l’URL de l’exemple dans cet article). Si vous consultez une page similaire à celle-ci, vous avez installé des tomcat7 correcte.
![][16]


###<a name="install-other-tomcat-components"></a>Installer d’autres composants de Tomcat
D’autres composants de tomcat facultatif que vous pouvez également installer.  

La commande **sudo apt-cache recherche tomcat7** permet d’afficher tous les composants disponibles. Les commandes suivantes sont des exemples d’installer certaines parties utiles.  

    sudo apt-get install tomcat7-admin      #admin web applications
    sudo apt-get install tomcat7-user         #tools to create user instances  

##<a name="phase-4-configure-tomcat"></a>Phase 4 : Configurer Tomcat
Dans cette phase, vous administrez tomcat.
###<a name="start-and-stop-tomcat7"></a>Démarrer et arrêter la tomcat7
Le serveur tomcat7 démarre automatiquement lorsque vous l’installez. Vous pouvez également le lancer vous-même avec la commande suivante :   

    sudo /etc/init.d/tomcat7 start

Pour arrêter la tomcat7 :  

    sudo /etc/init.d/tomcat7 stop

Pour afficher l’état de tomcat7 :  

    sudo /etc/init.d/tomcat7 status

Pour redémarrer les services de tomcat :  

    sudo /etc/init.d/tomcat7 restart

###<a name="tomcat-administration"></a>Administration de Tomcat
Vous pouvez modifier le fichier de configuration Tomcat utilisateur pour configurer vos informations d’identification d’administration avec la commande suivante :  

    sudo vi  /etc/tomcat7/tomcat-users.xml   

Voici un exemple :  
![][17]  

>AZURE. Remarque : Créer un mot de passe pour le nom d’utilisateur admin.  

Après avoir modifié ce fichier, vous devez redémarrer les services de tomcat7 avec la commande suivante pour vous assurer que les modifications prennent effet :  

    sudo /etc/init.d/tomcat7 restart  

Ouvrez votre navigateur et entrez l’URL **http://<your tomcat server DNS name>html/manager/**. Dans l’exemple de cet article, l’URL est http://tomcatexample.cloudapp.net/manager/html.  

Une fois connecté, vous devriez voir quelque chose de similaire à ce qui suit :  
![][18]

##<a name="common-issues"></a>Problèmes courants

###<a name="cant-access-the-virtual-machine-with-tomcat-and-moodle-from-the-internet"></a>Ne peut pas accéder à l’ordinateur virtuel avec Tomcat et Moodle à partir d’Internet

-   **Symptôme**  
Tomcat est en cours d’exécution, mais vous ne voyez pas la page par défaut de Tomcat avec votre navigateur.
-   **Cas possible de racine**   
    1.  Le port d’écoute de tomcat n’est pas identique au Port privé du point de terminaison de votre machine virtuelle pour le trafic de tomcat.  

        Vérifiez vos paramètres de point de terminaison ports du Public et privé et assurez-vous que le Port privé est le que même que le service tomcat port d’écoute. Reportez-vous à la section Phase 1 : Créer une Image pour obtenir des instructions sur la configuration des points de terminaison de votre machine virtuelle.  

        Pour déterminer le port d’écoute de tomcat, ouvrez /etc/httpd/conf/httpd.conf (version de Red Hat) ou /etc/tomcat7/server.xml (version Debian). Par défaut, le port d’écoute de tomcat est 8080. Voici un exemple :  

            <Connector port="8080" protocol="HTTP/1.1"  connectionTimeout="20000"  URIEncoding="UTF-8"            redirectPort="8443" />  

        Si vous utilisez un ordinateur virtuel comme Debian ou Ubuntu et que vous souhaitez modifier la valeur par défaut port de Tomcat écouter (par exemple, 8081), vous devez également ouvrir le port pour le système d’exploitation. Tout d’abord, ouvrez le profil :  

            sudo vi /etc/default/tomcat7  

        Supprimez les commentaires de la dernière ligne, puis modifier « non » sur « yes ».  

            AUTHBIND=yes

    2.  Le pare-feu a désactivé le port d’écoute de tomcat.

        Si vous ne pouvez voir que la page par défaut de Tomcat à partir de l’hôte local, le problème est très probablement que le port écouté par tomcat est bloqué par le pare-feu. Vous pouvez utiliser l’outil w3m pour parcourir la page web. Les commandes suivantes installer w3m, accédez à la page par défaut de Tomcat :  

            sudo yum  install w3m w3m-img
            w3m http://localhost:8080  

-   **Solution**
    1. Si le service tomcat écoute port n’est pas identique au Port privé du point de terminaison pour le trafic vers l’ordinateur virtuel, vous devez modifier le Port privé pour être que le même que le service tomcat port d’écoute.   

    2.  Si le problème est provoqué par le pare-feu/iptables, ajoutez les lignes suivantes à /etc/sysconfig/iptables :  

            -A INPUT -p tcp -m tcp --dport 80 -j ACCEPT
            -A INPUT -p tcp -m tcp --dport 443 -j ACCEPT  

        Notez que la deuxième ligne est uniquement nécessaire pour le trafic https.  

        Assurez-vous que c’est au-dessus de toutes les lignes qui seraient globalement restreindre l’accès, telles que les suivantes :  

            -A INPUT -j REJECT --reject-with icmp-host-prohibited  

        Pour recharger l’iptables, exécutez la commande suivante :  

            service iptables restart  

        Il a été testé sur CentOS 6.3.

###<a name="permission-denied-when-upload-you-project-files-to-varlibtomcat7webapps"></a>Autorisation refusée lorsque vous /var/lib/tomcat7/webapps des fichiers de projet de téléchargement /  

-   **Symptôme**  
Lorsque vous utilisez n’importe quel client SFTP (par exemple, FileZilla) pour se connecter à votre machine virtuelle et naviguez jusqu'à /var/lib/tomcat7/webapps/pour publier votre site, vous obtenez un message d’erreur semblable au suivant :  

        status: Listing directory /var/lib/tomcat7/webapps
        Command:    put "C:\Users\liang\Desktop\info.jsp" "info.jsp"
        Error:  /var/lib/tomcat7/webapps/info.jsp: open for write: permission denied
        Error:  File transfer failed

-   **Cas possible de racine** Vous n’avez aucuns autorisations d’accès au dossier /var/lib/tomcat7/webapps.  
-   **Solution**  
Vous devez obtenir l’autorisation de compte racine. Vous pouvez modifier la propriété de ce dossier à partir de racine pour le nom d’utilisateur que vous avez utilisée lors de la mise en service de l’ordinateur. Voici un exemple avec le nom du compte azureuser :  

        sudo chown azureuser -R /var/lib/tomcat7/webapps

    Utilisez l’option-R pour appliquer les autorisations de tous les fichiers dans un répertoire de trop.  

    Notez que cette commande fonctionne également pour les répertoires. L’option-R modifie les autorisations pour tous les fichiers et les répertoires dans le répertoire. Voici un exemple :  

        sudo chown -R username:group directory  

    Cette commande modifie la propriété (de l’utilisateur et de groupe) pour tous les fichiers et les répertoires dans le répertoire et le répertoire lui-même.  

    La commande suivante modifie l’autorisation d’accès du répertoire dossier uniquement mais laisse les fichiers et dossiers à l’intérieur de l’annuaire seul.  

        sudo chown username:group directory



[1]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-01.png
[2]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-02.png
[3]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-03.png
[4]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-04.png
[5]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-05.png
[6]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-06.png
[7]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-07.png
[8]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-08.png
[9]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-09.png
[10]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-10.png
[11]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-11.png
[12]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-12.png
[13]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-13.png
[14]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-14.png
[15]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-15.png
[16]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-16.png
[17]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-17.png
[18]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-18.png
