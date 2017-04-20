<properties 
    pageTitle="Serveur d’authentification à plusieurs facteurs d’authentification Windows et Azure"
    description="Il s’agit de la page d’authentification Azure plusieurs facteurs qui les aidera dans le déploiement de l’authentification Windows et serveur de l’authentification multifactorielle Azure."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="windows-authentication-and-azure-multi-factor-authentication-server"></a>Serveur d’authentification à plusieurs facteurs d’authentification Windows et Azure

La section de l’authentification Windows permet à l’administrateur activer et configurer l’authentification Windows pour une ou plusieurs applications.  Voici une liste de choses à prendre en compte avant l’installation de l’authentification Windows.

-  redémarrage est nécessaire avant que l’authentification à plusieurs facteurs Azure pour les Services Terminal Server sera appliquée.
-  Si « Match d’utilisateur requièrent l’authentification selon plusieurs facteurs Azure » est activée et que vous n’êtes pas dans la liste des utilisateurs, vous ne pourrez pas vous connecter à l’ordinateur après le redémarrage.
-  Adresses IP autorisées dépend de si l’application peut fournir l’IP du client avec l’authentification. Actuellement, seuls les Services Terminal Server est pris en charge.  







>[AZURE.NOTE]Cette fonctionnalité n’est pas pris en charge pour sécuriser des Services Terminal Server sur Windows Server 2012 R2.




## <a name="to-secure-an-application-with-windows-authentication-use-the-following-procedure"></a>Pour sécuriser une application avec l’authentification Windows, utilisez la procédure suivante.

1. Dans le serveur de l’authentification multifactorielle Azure, cliquez sur l’icône de l’authentification Windows.
![Authentification Windows](./media/multi-factor-authentication-get-started-server-windows/windowsauth.png)
2. Case à cocher Activer Windows authentication. Par défaut, cette case est désactivée.
3. L’onglet Applications permet à l’administrateur de configurer une ou plusieurs applications pour l’authentification Windows.
4. Sélectionnez un serveur ou une application – permet de spécifier si l’application/serveur est activée. Cliquez sur OK.
5. Cliquez sur Ajouter... bouton.
6. L’onglet IPs de confiance vous permet d’ignorer les sessions Windows Azure plusieurs facteurs d’authentification pour provenant de certaines adresses IP spécifiques. Par exemple, si les employés utilisent l’application à partir du bureau et à domicile, vous pouvez décider de vous ne voulez pas que leur téléphone sonne pour une authentification à facteurs multiples Azure au bureau. Pour ce faire, vous devez spécifier le sous-réseau d’office en tant qu’entrée de l’IPs de confiance.
7. Cliquez sur Ajouter... bouton.
8. Sélectionnez l’adresse IP unique si vous souhaitez ignorer une seule adresse IP.
9. Sélectionnez la plage d’adresses IP si vous souhaitez ignorer une plage IP entière. 10.63.193.1-10.63.193.100 de l’exemple.
10. Sélectionnez le sous-réseau que vous souhaitez spécifier une plage d’adresses IP à l’aide de la notation du sous-réseau. Entrez l’adresse IP de début de sous-réseau et choisissez le masque de réseau approprié dans la liste déroulante.
11. Cliquez sur le bouton OK.
