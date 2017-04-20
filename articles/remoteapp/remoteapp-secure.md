
<properties
    pageTitle="Sécuriser les applications et les ressources dans Azure RemoteApp | Microsoft Azure"
    description="Découvrez comment verrouiller les applications et les ressources dans Azure RemoteApp"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="secure-apps-and-resources-in-azure-remoteapp"></a>Sécuriser les applications et les ressources dans Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

RemoteApp Azure fournit aux utilisateurs des accès aux géré de façon centralisée des applications de Windows, ce qui vous permet de contrôler ce que vos utilisateurs peuvent et ne peut pas faire.  Ceci est particulièrement utile lorsque l’utilisateur se connecte à partir d’un périphérique non managé (par exemple leur Macbook personnel) et vous souhaitez contrôler l’accès de l’utilisateur ou l’expérience.

Par exemple, si vous utilisez Active Directory pour l’authentification des utilisateurs et que vous souhaitez empêcher les utilisateurs de copier des données d’une application, vous pouvez configurer une stratégie de groupe Bureau à distance pour empêcher les utilisateurs de copier des données.

Un autre exemple est si vous souhaitez bloquer l’accès internet à une application spécifique dans votre collection. Vous pouvez créer une règle de pare-feu de Windows bloque l’accès lorsque vous créez l’image de votre collection.

## <a name="implementation-options"></a>Options d’implémentation

  Voici les options de mise en œuvre de clé, qui peuvent être utilisées individuellement ou en tandem en fonction des besoins :

1.  Si votre collection de RemoteApp est joint au domaine, vous pouvez appliquer une [Stratégie de groupe](https://technet.microsoft.com/library/cc725828.aspx) (à l’exception de la Idle et se déconnecter du délai d’attente stratégies décrites [ici](../azure-subscription-service-limits.md)).
2.  Comme alternative à la stratégie de groupe (si votre collection n’est pas joint au domaine, ou vous n’avez pas les privilèges de droite dans Active Directory), vous pouvez configurer les [Stratégies locales](https://technet.microsoft.com/library/cc775702.aspx) dans votre image du modèle.  Notez que les stratégies de groupe prévalent sur les stratégies locales lorsqu’il existe un conflit.
3.  Certains paramètres du système d’exploitation/app ne sont pas configurables via la stratégie, mais peuvent être via la clé de Registre à l’aide de l' [outil RegEdit](./remoteapp-hybridtrouble.md) lors de la configuration de votre image du modèle.
4.  Vous pouvez utiliser [Le pare-feu Windows](http://windows.microsoft.com/en-US/windows-8/Windows-Firewall-from-start-to-finish) pour contrôler l’accès réseau vers et à partir de l’ordinateur où l’application est en cours d’exécution. Veillez simplement à que ne pas bloquer les URL et les ports définis ici.
5.  Vous pouvez utiliser [AppLocker](https://technet.microsoft.com/library/hh831440.aspx) pour contrôler les applications et les fichiers que les utilisateurs peuvent exécuter. Par exemple, les utilisateurs expérimentés peuvent déterminer comment exécuter les applications que vous n’avez pas publié mais qui sont disponibles dans l’image que vous avez utilisé pour créer la collection - AppLocker peut bloquer ce.

## <a name="detailed-information"></a>Informations détaillées

- Les stratégies RDS suivantes sont susceptibles d’être plus utiles :
    - [Périphériques et des ressources](https://technet.microsoft.com/library/ee791794.aspx)
    - [Redirection d’imprimante](https://technet.microsoft.com/library/ee791784.aspx)
    - [Les profils](https://technet.microsoft.com/library/ee791865.aspx).
- Notez que la configuration des redirections via RemoteApp PowerShell module (tel que vu [ici](./remoteapp-redirection.md)) repose sur la machine cliente pour appliquer la stratégie, donc si la sécurité est le principal objectif essentiel appliquer la stratégie via la stratégie modèle d’image locale ou stratégie de groupe.
- [Les stratégies Windows Server 2012 R2](https://technet.microsoft.com/library/hh831791.aspx).
- [Stratégies d’Office 2013](https://technet.microsoft.com/library/cc178969.aspx) (y compris [la personnalisation de la barre d’outils Office](https://technet.microsoft.com/library/cc179143.aspx)).
