
<properties
    pageTitle="Exigences de l’application pour Azure RemoteApp | Microsoft Azure"
    description="En savoir plus sur la configuration requise pour les applications que vous souhaitez utiliser dans Azure RemoteApp"
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



# <a name="app-requirements"></a>Exigences de l’application

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

RemoteApp Azure prend en charge les applications en continu 32 bits ou 64 bits basé sur Windows à partir d’une image Windows Server 2012 R2. La plupart des 32 bits ou 64 bits basées sur Windows applications existantes exécutées « tel quel » dans Azure RemoteApp (Services Bureau à distance ou anciennement nommé Services Terminal Server) environnement. Toutefois, il existe une différence entre l’exécution et la marche : certaines applications fonctionnent correctement et fonctionnent correctement, alors que d’autres ne le font pas. Les informations suivantes fournissent des conseils pour le développement d’applications dans un environnement de Services Bureau à distance et de test pour assurer sa compatibilité.

Conseil : Nous travaillons sur la création de quelques exemples d’utilisation des applications pour vous. Vous verrez les nouvelles rubriques qui traitent de l’utilisation de Microsoft Access, QuickBooks et App-V dans RemoteApp.

## <a name="requirements"></a>Configuration requise
Ces trois conditions, si, à votre application s’exécutent correctement dans RemoteApp :

1.  Les applications qui répondent à toutes les [exigences de Certification pour les applications de bureau Windows](https://msdn.microsoft.com/library/windows/desktop/hh749939.aspx) et à respectent les [instructions de programmation des Services Bureau à distance](https://msdn.microsoft.com/library/aa383490.aspx) aura une compatibilité totale avec RemoteApp.
2.  Les applications doivent jamais stocker des données localement sur l’image ou les instances de RemoteApp qui risquent d’être perdues.  Après avoir créé une collection de RemoteApp, les instances sont clonés et sont sans état et ne doivent contenir que des applications. Stocker des données d’une source externe, ou dans le profil de l’utilisateur.
3.  Des images personnalisées ne doivent jamais contenir de données peuvent être perdues.  

## <a name="testing-your-apps"></a>Test de vos applications
Utilisez les étapes suivantes pour tester les applications :

1.  Installation de Windows Server 2012 R2 et votre application
2.  Activer le Bureau à distance
3.  Créer deux comptes d’utilisateurs, utilisateur a et à l’UtilisateurB, ajout de deux comptes d’utilisateur au groupe de sécurité de bureau à distance.
4.  Vérification de la compatibilité de plusieurs session par l’établissement de deux sessions RDS simultanées à l’ordinateur lors du lancement de l’application.
5.  Contrôler le comportement de l’application

## <a name="application-development-guidelines"></a>Instructions de développement d’applications
Utilisez les instructions suivantes pour développer des applications de RemoteApp.

### <a name="multiple-users"></a>Plusieurs utilisateurs

- Installation d’une [application pour un utilisateur unique ](https://msdn.microsoft.com/library/aa380661.aspx)peut créer des problèmes dans un environnement multi-utilisateur.
- Les applications doivent [stocker des informations spécifiques à l’utilisateur](https://msdn.microsoft.com/library/aa383452.aspx) dans des emplacements spécifiques à l’utilisateur, séparément de globale de l’information qui s’applique à tous les utilisateurs.
- RemoteApp utilise plusieurs [espaces de noms pour les objets du noyau](https://msdn.microsoft.com/library/aa382954.aspx); un espace de noms global est principalement utilisée par les services dans les applications client/serveur.
- Il n’est pas prudent de supposer que le nom de l’ordinateur ou l' [adresse IP](https://msdn.microsoft.com/library/aa382942.aspx) attribuée à l’ordinateur sont associés à un seul utilisateur, car plusieurs utilisateurs peuvent être connectés simultanément à un serveur hôte de Session Bureau à distance (hôte de Session Bureau à distance).

### <a name="performance"></a>Performances
- Désactiver les [effets](https://msdn.microsoft.com/library/aa380822.aspx) avant d’ajouter votre application à RemoteApp.
- Pour optimiser la disponibilité du processeur pour tous les utilisateurs, désactivez les [tâches en arrière-plan](https://msdn.microsoft.com/library/aa380665.aspx) ou créer des tâches d’arrière-plan efficace qui ne sont pas beaucoup de ressources.
- Vous devez régler et équilibrer [l’utilisation des threads](https://msdn.microsoft.com/library/aa383520.aspx) des applications dans un environnement multi-utilisateur, plusieurs processeurs.
- Pour optimiser les performances, il est recommandé pour les applications de [détecter](https://msdn.microsoft.com/library/aa380798.aspx) si elles sont en cours d’exécution dans une session client.
