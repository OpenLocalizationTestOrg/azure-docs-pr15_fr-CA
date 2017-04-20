<properties 
    pageTitle="Comment faire pour installer Apache Qpid PROTONS-C sur une VM Linux | Microsoft Azure"
    description="Comment créer une VM de Linux CentOS à l’aide d’ordinateurs virtuels Azure et explique comment générer et installer la bibliothèque Apache Qpid PROTONS-C."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" /> 
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/29/2016"
    ms.author="sethm" />

# <a name="install-apache-qpid-proton-c-on-an-azure-linux-vm"></a>Installer Apache Qpid PROTONS-C sur une machine virtuelle de Linux Azure

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Cette section indique comment créer une VM de Linux CentOS à l’aide d’ordinateurs virtuels Azure et comment télécharger, créer et installer la bibliothèque Apache Qpid PROTONS-C ainsi que les liaisons de langage Python et PHP. Une fois ces étapes effectuées, vous serez en mesure d’exécuter les exemples de Python et PHP inclus dans ce guide.

La première étape est exécutée à l’aide d' [Azure portal classique][]. La capture d’écran suivante illustre la création d’une machine virtuelle de CentOS nommé « scott-centos » :

![PROTONS sur une machine virtuelle de Linux Azure][0]

Après le déploiement, le portail affiche les éléments suivants :

![PROTONS sur une machine virtuelle de Linux Azure][1]

Pour vous connecter à l’ordinateur, vous devez connaître le port de point de terminaison pour SSH. Vous pouvez obtenir cette valeur à partir du [Azure portal classique][] en sélectionnant l’ordinateur virtuel nouvellement créé, puis cliquez sur l’onglet **points de terminaison** . La capture d’écran suivante montre que la voie SSH publique pour cet ordinateur est 57146.

![PROTONS sur une machine virtuelle de Linux Azure][2]

Vous pouvez maintenant vous connecter à l’ordinateur à l’aide de SSH. Cet exemple utilise l’outil mastic, comme dans la capture d’écran suivante :

![PROTONS sur une machine virtuelle de Linux Azure][3]

Pour les applications PHP et les Python, cet exemple utilise les bibliothèques clientes de PROTONS d’Apache. Ces bibliothèques sont disponibles pour téléchargement à partir de [http://qpid.apache.org/download.html](http://qpid.apache.org/download.html). Le fichier Lisezmoi dans le package de distribution explique les étapes requises pour installer les dépendances et générer de PROTONS. Voici un résumé des étapes :

1.  Modifier le fichier de configuration d’yum (/ etc/yum.conf) et commentez l’exclusion des mises à jour aux en-têtes de noyau (\# exclure = noyau\*). Il est nécessaire d’installer le compilateur gcc.

2.  Installer les packages de composants requis :

    ```
    # required dependencies 
    yum install gcc cmake libuuid-devel
    
    # dependencies needed for ssl support
    yum install openssl-devel
    
    # dependencies needed for bindings
    yum install swig python-devel ruby-devel php-devel java-1.6.0-openjdk
    
    # dependencies needed for python docs
    yum install epydoc
    ```

1.  Télécharger la bibliothèque de PROTONS :

    ```
    [azureuser@this-user ~]$ wget http://apache.panu.it/qpid/proton/0.9/qpid-proton-0.9.tar.gz
    --2016-04-17 14:45:03--  http://apache.panu.it/qpid/proton/0.9/qpid-proton-0.9.tar.gz
    Resolving apache.panu.it (apache.panu.it)... 81.208.22.71
    Connecting to apache.panu.it (apache.panu.it)|81.208.22.71|:80... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: 868226 (848K) [application/x-gzip]
    Saving to: ‘qpid-proton-0.9.tar.gz’
    
    qpid-proton-0.9.tar.gz                               
    
    100%[====================================================================================================================>] 847.88K   102KB/s    in 8.4s    
    
    2016-04-17 14:45:12 (101 KB/s) - ‘qpid-proton-0.9.tar.gz’ saved [868226/868226]
    ```

1.  Extraire le code de PROTONS à partir de l’archive de distribution :

    ```
    tar xvfz qpid-proton-0.9.tar.gz
    ```

1.  Générer et installer le code qui suit, extrait du fichier Lisezmoi :

    ```
    From the directory where you found this README file:    
    
    mkdir build cd build
            
    # Set the install prefix. You may need to adjust depending on your      
    # system.       
    cmake -DCMAKE\_INSTALL\_PREFIX=/usr ..
            
    # Omit the docs target if you do not wish to build or install       
    # documentation.        
    make all docs
            
    # Note that this step will require root privileges.     
    make install
    ```

Après avoir effectué ces étapes, les PROTONS est installé sur l’ordinateur et prêt à l’emploi.

## <a name="next-steps"></a>Étapes suivantes

Prêt pour en savoir plus ? Consultez le lien suivant :

- [Vue d’ensemble du service Bus AMQP][]

[Vue d’ensemble du service Bus AMQP]: service-bus-amqp-overview.md
[0]: ./media/service-bus-amqp-apache/amqp-apache-1.png
[1]: ./media/service-bus-amqp-apache/amqp-apache-2.png
[2]: ./media/service-bus-amqp-apache/amqp-apache-3.png
[3]: ./media/service-bus-amqp-apache/amqp-apache-4.png

[Azure portal classique]: http://manage.windowsazure.com


