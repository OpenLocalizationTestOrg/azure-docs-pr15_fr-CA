<properties 
    pageTitle="Passerelle des services Bureau à distance et Azure plusieurs facteurs d’authentification serveur, à l’aide de RADIUS"
    description="Il s’agit de la page d’authentification Azure plusieurs facteurs qui les aide à déployer une passerelle de bureau à distance (RD) et le serveur d’authentification selon plusieurs facteurs Azure à l’aide de RADIUS."
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
    ms.date="08/15/2016"
    ms.author="kgremban"/>

# <a name="remote-desktop-gateway-and-azure-multi-factor-authentication-server-using-radius"></a>Passerelle des services Bureau à distance et Azure plusieurs facteurs d’authentification serveur, à l’aide de RADIUS

Dans de nombreux cas, la passerelle Bureau à distance utilise le serveur NPS local pour authentifier les utilisateurs. Ce document décrit comment router les demandes RADIUS à partir de la passerelle Bureau à distance (via le serveur NPS local) vers le serveur de l’authentification multifactorielle.

Le serveur d’authentification selon plusieurs facteurs doit être installé sur un serveur distinct, qui sera ensuite proxy la requête RADIUS sur le serveur NPS sur le serveur de passerelle Bureau à distance. Une fois NPS valide le nom d’utilisateur et le mot de passe, il renvoie une réponse au serveur d’authentification selon plusieurs facteurs qui effectue le second facteur d’authentification avant de retourner un résultat à la passerelle.





## <a name="configure-the-rd-gateway"></a>Configuration de la passerelle des services Bureau à distance

La passerelle des services Bureau à distance doit être configurée pour envoyer l’authentification RADIUS à un serveur de l’authentification multifactorielle Azure. Une fois la passerelle des services Bureau à distance a été installé, configuré et travail, allez aux propriétés de la passerelle des services Bureau à distance. Cliquez sur l’onglet magasin de stratégies de bureau à distance et le modifier pour utiliser un serveur Central exécutant NPS au lieu du serveur Local exécutant NPS. Ajouter un ou plusieurs serveurs de l’authentification multicritères de Azure en tant que serveurs RADIUS et spécifiez un secret partagé pour chaque serveur.





## <a name="configure-nps"></a>Configurer NPS

La passerelle des services Bureau à distance utilise NPS pour envoyer la demande RADIUS d’authentification à plusieurs facteurs Azure. Un délai d’attente doit être modifié pour empêcher la passerelle Bureau à distance à partir d’arriver à expiration avant la fin de l’authentification multifactorielle. Utilisez la procédure suivante pour configurer NPS.

1. Dans NPS, développez le menu Clients RADIUS et le serveur, dans la colonne de gauche et cliquez sur les groupes de serveurs RADIUS distants. Allez dans les propriétés du groupe du serveur de passerelle TS. Modifier l’ou les serveurs RADIUS affiché et accédez à l’onglet de l’équilibrage de charge. Modifier le « délai d’expiration sans réponse avant que la demande est considérée comme interrompue » et le « nombre de secondes entre les demandes avant que le serveur est identifié comme non disponible » de 30 à 60 secondes. Cliquez sur l’onglet authentification/compte et vous assurer que les ports RADIUS spécifiés correspondent les ports qui seront à l’écoute sur le serveur d’authentification selon plusieurs facteurs.
2. NPS doit également être configuré pour recevoir des authentifications RADIUS du serveur de l’authentification multifactorielle Azure. Dans le menu de gauche, cliquez sur Clients RADIUS. Ajoutez le serveur de l’authentification multifactorielle Azure comme un client RADIUS. Choisissez un nom convivial et spécifiez un secret partagé.
3. Développez la section stratégies de la navigation de gauche, puis cliquez sur stratégies de demande de connexion. Il doit contenir une stratégie de demande de connexion appelée stratégie d’autorisation de passerelle TS qui a été créé lors de la configuration de la passerelle des services Bureau à distance. Cette stratégie transfère les demandes RADIUS sur le serveur d’authentification selon plusieurs facteurs.
4. Copier cette stratégie pour créer un nouveau. Dans la nouvelle stratégie, ajouter une condition qui correspond au nom convivial du Client avec le nom convivial est définie à l’étape 2 ci-dessus pour le client Azure plusieurs facteurs d’authentification serveur RADIUS. Modifier le fournisseur d’authentification de l’ordinateur Local. Cette stratégie garantit que lorsqu’une requête RADIUS est reçue à partir du serveur de l’authentification multifactorielle Azure, l’authentification se produit localement au lieu d’envoyer une requête RADIUS au serveur d’authentification selon plusieurs facteurs Azure entraînant une condition de boucle. Pour éviter une condition de boucle, cette nouvelle stratégie doit être commandée au-dessus de la stratégie d’origine qui transfère au serveur d’authentification selon plusieurs facteurs.

## <a name="configure-azure-multi-factor-authentication"></a>Configurer l’authentification selon plusieurs facteur Azure


--------------------------------------------------------------------------------



Le serveur de l’authentification multifactorielle Azure est configuré en tant que proxy RADIUS entre la passerelle des services Bureau à distance et serveur NPS.  Il doit être installé sur un serveur à un domaine qui est différent du serveur de passerelle Bureau à distance. Utilisez la procédure suivante pour configurer le serveur de l’authentification multifactorielle Azure.

1. Le serveur de l’authentification multifactorielle Azure, cliquez sur l’icône de l’authentification RADIUS. Case à cocher Activer le RADIUS d’authentification.
2. Sous l’onglet Clients, assurez-vous que les ports correspondent à ce qui est configuré dans NPS et cliquez sur Ajouter... bouton. Ajoutez l’adresse IP du serveur passerelle des services Bureau à distance, nom de l’application (facultatif) et un secret partagé. Vous devez être le même sur le serveur de l’authentification multifactorielle Azure et passerelle Bureau à distance le secret partagé.
3. Cliquez sur l’onglet cible et cliquez sur le bouton radio du ou des serveurs RADIUS.
4. Cliquez sur Ajouter... bouton. Entrez l’adresse IP, secret partagé et les ports du serveur NPS. À moins d’utiliser un serveur NPS central, le client RADIUS et la cible RADIUS sera le même. Le secret partagé doit correspondre à l’une configuration dans la section du client RADIUS du serveur NPS.

![Authentification RADIUS](./media/multi-factor-authentication-get-started-server-rdg/radius.png)
