<properties 
    pageTitle="Découverte de l’application des paramètres de Registre pour les Services de Proxy du nuage | Microsoft Azure" 
    description="L’objectif de cette rubrique est de vous fournir les étapes que vous devez effectuer pour définir le port requis sur les ordinateurs exécutant l’agent de découverte d’application Cloud." 
    services="active-directory" 
    documentationCenter="" 
    authors="markusvi" 
    manager="femila"/>

<tags 
    ms.service="active-directory" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016" 
    ms.author="markusvi"/>

# <a name="cloud-app-discovery-registry-settings-for-proxy-services"></a>Paramètres de Registre de découverte d’application cloud pour les Services de Proxy

Par défaut, l’agent de découverte d’application Cloud est configuré pour utiliser uniquement les ports 80 ou 443. Si vous envisagez l’installation de découverte d’application Cloud dans un environnement avec un serveur proxy qui utilise un port personnalisé (443 ni 80), vous devez configurer vos agents pour utiliser ce port. La configuration est basée sur une clé de Registre.


L’objectif de cette rubrique est de vous fournir les étapes que vous devez effectuer pour définir le port requis sur les ordinateurs exécutant l’agent de découverte d’application Cloud.



**Pour modifier le port utilisé par l’ordinateur qui exécute l’agent de découverte d’application Cloud, effectuez les opérations suivantes :**


1. Démarrez l’Éditeur du Registre. <br> ![Exécuter](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy01.png)

2. Accédez à ou créez la clé de Registre suivante : <br> **Discovery\Endpoint d’application HKLM_LOCAL_MACHINE\Software\Microsoft\Cloud** 

3. Créez une nouvelle valeur de **chaînes multiples** appelée **Ports**. ![Nouveau](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy02.png)

4. Pour ouvrir la boîte de dialogue **Modifier les chaînes multiples** , double-cliquez sur la valeur de Ports.


5. Dans la zone données de la valeur, tapez les valeurs suivantes et ajoutez tous les ports personnalisés qui sont utilisés par votre organisation : <br><br>
**80** <br>
**8080** <br>
**8118** <br>
**8888** <br>
**81** <br>
**12080** <br>
**6999** <br>
**30606** <br>
**31595** <br>
**4080** <br>
**443** <br>
**1110** <br><br>
![Modifier les chaînes multiples](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy03.png)

6. Cliquez sur **OK** pour fermer la boîte de dialogue **Modifier les chaînes multiples** .



**Ressources supplémentaires**


* [Comment puis-je connaître les applications cloud non sanctionnée utilisés au sein de mon organisation](active-directory-cloudappdiscovery-whatis.md) 


