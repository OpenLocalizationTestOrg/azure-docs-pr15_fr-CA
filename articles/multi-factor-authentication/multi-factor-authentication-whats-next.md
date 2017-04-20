<properties
    pageTitle="Authentification à plusieurs facteurs Azure - quel est l’avenir"
    description="Il s’agit de la page d’authentification multifactorielle d’Azure qui décrit la procédure à suivre avec AMF.  Cela inclut les rapports, alerte à la fraude, contournement unique, les messages vocaux personnalisés, la mise en cache, les mots de passe ips et d’application de confiance."
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
    ms.topic="article"
    ms.date="09/23/2016"
    ms.author="kgremban"/>

# <a name="configuring-azure-multi-factor-authentication"></a>Configuration de l’authentification multifactorielle Azure

Cet article vous permet de gérer l’authentification à plusieurs facteurs Azure maintenant que vous êtes en cours d’exécution.  Il traite de nombreuses rubriques qui vous aident à tirer le meilleur parti de l’authentification multifactorielle Azure.  Toutes ces fonctionnalités sont disponibles dans chaque version de l’authentification à plusieurs facteurs Azure.

La configuration de certaines des fonctionnalités ci-dessous se trouve dans le portail de gestion de l’authentification multifactorielle Azure. Il existe deux méthodes différentes que vous pouvez accéder au portail de gestion d’AMF, qui sont effectuées via le portail Azure. Le premier est en gérant un fournisseur d’authentification multifactorielle si l’aide en fonction de la consommation de l’AMF. La seconde est via les paramètres de service AMF. La seconde option nécessite soit une licence Azure AMF, Azure AD Premium ou Suite de mobilité d’entreprise ou un fournisseur d’authentification selon plusieurs facteurs.

Pour accéder au portail de gestion AMF via un fournisseur d’authentification selon plusieurs facteurs Azure, connectez-vous au portail Azure en tant qu’administrateur et sélectionnez l’option de Active Directory. Cliquez sur l’onglet **Fournisseurs d’authentification selon plusieurs facteurs** , puis le répertoire et cliquez sur le bouton **Gérer** en bas.

Pour accéder au portail de gestion AMF via la page Paramètres du Service AMF, connectez-vous au portail Azure en tant qu’administrateur et sélectionnez l’option de Active Directory. Cliquez sur le répertoire et puis cliquez sur l’onglet **configurer** . Dans la section authentification à facteurs multiples, sélectionnez **Gérer les paramètres de service**. En bas de la page Paramètres du Service AMF, cliquez sur le lien **accéder au portail** .


Fonctionnalité| Description| Ce qui est couvert
:------------- | :------------- | :------------- |
[Alerte à la fraude](#fraud-alert)|Alerte de fraude pouvant être configuré et configurer afin que vos utilisateurs peuvent signaler frauduleux tente d’accéder à leurs ressources.|Comment faire pour définir, configurer et signaler les fraudes
[Contournement unique](#one-time-bypass) |Une dérivation unique permet à un utilisateur pour s’authentifier une seule fois par plusieurs facteurs d’authentification « ignorer ».|Comment faire pour installer et configurer un contournement unique
[Messages vocaux personnalisés](#custom-voice-messages) |Messages vocaux personnalisés permettent d’utiliser vos propres enregistrements ou les salutations avec authentification à plusieurs facteurs. |Comment faire pour installer et configurer des messages et des salutations personnalisées
[La mise en cache](#caching-in-azure-multi-factor-authentication)|La mise en cache vous permet de définir une heure spécifique de période afin que les tentatives d’authentification suivantes réussissent automatiquement. |Comment installer et configurer la mise en cache de l’authentification.
[Adresses IP autorisées](#trusted-ips)|Approuvé Qu'ips est une fonction de plusieurs facteurs d’authentification qui permet aux administrateurs d’un client géré ou fédéré ignorer plusieurs facteurs d’authentification pour les utilisateurs qui sont la signature à partir de l’intranet local de la société.|Configurer et paramétrer les adresses IP qui sont exemptés de l’authentification multicritères
[Application de mots de passe](#app-passwords)|Un mot de passe d’application permet à une application qui ne connaît pas AMF-pour contourner l’authentification à facteurs multiples et continuer à travailler.|Informations sur les mots de passe app.
[N’oubliez pas d’authentification à plusieurs facteurs pour les navigateurs et périphériques mémorisées](#remember-multi-factor-authentication-for-devices-users-trust)|Vous permet de garder à l’esprit les périphériques pour un nombre défini de jours après qu’un utilisateur est connecté avec succès à l’aide d’AMF.|Informations sur l’activation de cette fonctionnalité et configurer le nombre de jours.
[Méthodes de vérification sélectionnables](#selectable-verification-methods)|Vous permet de choisir les méthodes d’authentification disponibles pour les utilisateurs à utiliser.|Informations sur l’activation ou la désactivation des méthodes d’authentification spécifiques tels que des messages d’appel ou de texte.



## <a name="fraud-alert"></a>Alerte à la fraude
Alerte de fraude pouvant être configuré et configurer afin que vos utilisateurs peuvent signaler frauduleux tente d’accéder à leurs ressources.  Les utilisateurs peuvent signaler la fraude avec l’application mobile ou par le biais de leur téléphone.

### <a name="to-set-up-and-configure-fraud-alert"></a>Pour installer et configurer l’alerte de fraude

1.  Ouvrez une session sur http://azure.microsoft.com
2.  Naviguez vers le portail de gestion AMF par les instructions en haut de cette page.
3.  Dans le portail de gestion Azure plusieurs facteurs d’authentification, cliquez sur paramètres dans la section de configuration.
4.  Sous la section alerte de fraude de la page Paramètres, vérifiez l’autoriser les utilisateurs pour envoyer des alertes de fraude.
5.  Si vous souhaitez que les utilisateurs soient bloqués lors de la fraude est signalée, activez bloquer l’utilisateur lors de la fraude est signalée.
6.  Dans la zone de texte **Code de rapport fraude pendant initiale de salutation** , saisissez un code qui peut être utilisé lors de la vérification de l’appel. Si un utilisateur entre ce code et la # au lieu de simplement le signe #, une alerte de fraude est signalée.
7.  En bas, cliquez sur Enregistrer.

>[AZURE.NOTE]
>D’accueil vocaux de Microsoft par défaut demander aux utilisateurs d’appuyer sur # 0 pour envoyer une alerte de fraude. Si vous souhaitez utiliser un code différent de 0, vous devez enregistrer et charger vos propres d’accueil vocaux personnalisés avec les instructions appropriées.


![Nuage](./media/multi-factor-authentication-whats-next/fraud.png)

### <a name="to-report-fraud-alert"></a>Alerte de fraude de rapport
Alerte de fraude peut être déclarée de deux façons.  Soit par le biais de l’application mobile ou par téléphone.  

### <a name="to-report-fraud-alert-with-the-mobile-app"></a>Alerte de fraude de rapport avec l’application mobile



1. Lorsqu’une vérification est envoyée sur votre téléphone, sélectionnez démarrage de l’application Microsoft Authenticator.
2. Pour signaler la fraude, cliquez sur Annuler et rapport de fraude. Cela affiche une boîte indiquant que le personnel de Support informatique de votre organisation est averti.
3. Cliquez sur les fraudes de rapport.
4. Dans l’application, cliquez sur Fermer.

![Nuage](./media/multi-factor-authentication-whats-next/report1.png)


![Nuage](./media/multi-factor-authentication-whats-next/fraud2.png)

### <a name="to-report-fraud-alert-with-the-phone"></a>Alerte de fraude de rapport avec le téléphone

1. Lorsqu’un appel de vérification arrive sur votre téléphone, répondez-y.  
2. Pour signaler la fraude, entrez le code qui a été configuré pour correspondre avec les rapports des fraudes via le téléphone, puis le signe #. Vous serez averti qu’une alerte de fraude a été soumise.
3. Terminer l’appel.

### <a name="to-view-the-fraud-report"></a>Pour afficher le rapport de fraude

1. Ouvrez une session sur [http://azure.microsoft.com](https://azure.microsoft.com/)
2. Sur la gauche, sélectionnez Active Directory.
3. Dans la partie supérieure, sélectionnez fournisseurs d’authentification selon plusieurs facteurs. Elle affiche une liste de vos fournisseurs d’authentification selon plusieurs facteurs.
4. Si vous avez plusieurs fournisseurs d’authentification multifactorielle, sélectionnez celui que vous souhaitez afficher l’état d’alerte de fraude et cliquez sur gérer au bas de la page. Si vous avez uniquement, cliquez sur Gérer. Le portail de gestion de l’authentification multifactorielle Azure s’ouvre.
5. Sur le portail de gestion de l’authentification à plusieurs facteurs de Azure, à gauche, sous affichage d’un rapport, cliquez sur l’alerte de fraude.
6. Spécifiez la plage de dates que vous souhaitez afficher dans le rapport. Vous pouvez également spécifier les noms d’utilisateur spécifiques, les numéros de téléphone et l’état de l’utilisateur.
7. Cliquez sur Exécuter. Un rapport similaire à celui ci-dessous s’affiche. Vous pouvez également cliquer sur Exporter au format CSV si vous souhaitez exporter le rapport.

## <a name="one-time-bypass"></a>Contournement unique

Une dérivation unique permet à un utilisateur pour s’authentifier une seule fois par plusieurs facteurs d’authentification « ignorer ». Le contournement est temporaire et expire après le nombre de secondes spécifié.  Donc dans les situations où l’application mobile ou un téléphone ne reçoit une notification ou un appel téléphonique, vous pouvez activer un contournement unique afin que l’utilisateur peut accéder à la ressource souhaitée.

### <a name="to-create-a-one-time-bypass"></a>Pour créer un contournement unique

1.  Ouvrez une session sur http://azure.microsoft.com
2.  Naviguez vers le portail de gestion AMF par les instructions en haut de cette page.
3.  L’Azure plusieurs facteurs d’authentification portail de gestion, si vous voyez le nom de votre client ou votre fournisseur d’AMF Azure sur la gauche avec un + en regard, cliquez sur le + voir les différents groupes de réplication de serveur de l’AMF et le groupe Azure par défaut. Cliquez sur le groupe approprié.
4.  Administration des utilisateurs, cliquez sur **Unique contournement**.
![Nuage](./media/multi-factor-authentication-whats-next/create1.png)
5.  Sur la page de contournement de l’unique, cliquez sur **Nouveau contournement d’unique**.
6.  Entrez le nom d’utilisateur, le nombre de secondes que le contournement existent, la raison de l’exception, cliquez sur **Ignorer**.
![Nuage](./media/multi-factor-authentication-whats-next/create2.png)
7.  À ce stade, l’utilisateur doit se connecter avant l’expiration de la dérivation unique.



### <a name="to-view-the-one-time-bypass-report"></a>Pour afficher le rapport de contournement unique

1. Ouvrez une session sur [http://azure.microsoft.com](https://azure.microsoft.com/)
2. Sur la gauche, sélectionnez Active Directory.
3. Dans la partie supérieure, sélectionnez fournisseurs d’authentification selon plusieurs facteurs. Elle affiche une liste de vos fournisseurs d’authentification selon plusieurs facteurs.
4. Si vous avez plusieurs fournisseurs d’authentification multifactorielle, sélectionnez celui que vous souhaitez afficher l’état d’alerte de fraude et cliquez sur gérer au bas de la page. Si vous avez uniquement, cliquez sur Gérer. Le portail de gestion de l’authentification multifactorielle Azure s’ouvre.
5. Dans le portail de gestion de l’authentification à plusieurs facteurs de Azure, à gauche, sous affichage d’un rapport, cliquez sur Ignorer d’unique.
6. Spécifiez la plage de dates que vous souhaitez afficher dans le rapport. Vous pouvez également spécifier les noms d’utilisateur spécifiques, les numéros de téléphone et l’état de l’utilisateur.
7. Cliquez sur Exécuter. Un rapport similaire à celui ci-dessous s’affiche. Vous pouvez également cliquer sur Exporter au format CSV si vous souhaitez exporter le rapport.

<center>![Nuage](./media/multi-factor-authentication-whats-next/report.png)</center>

## <a name="custom-voice-messages"></a>Messages vocaux personnalisés

Messages vocaux personnalisés permettent d’utiliser vos propres enregistrements ou les salutations avec authentification à plusieurs facteurs.  Ceux-ci peuvent être utilisés en plus de, ou pour remplacer Microsoft les enregistrements.

Avant de commencer à être conscient des éléments suivants :

- Les formats de fichier pris en charge en cours sont .wav et .mp3.
- La limite de taille de fichier est de 5 Mo.
- Il est recommandé pour l’authentification de messages qu’il pas plus de 20 secondes. Toute valeur supérieure à cela provoquerait la vérification échoue, car l’utilisateur peut ne pas répond avant que le message se termine et la vérification de l’expiration.



### <a name="to-set-up-custom-voice-messages-in-azure-multi-factor-authentication"></a>Pour configurer les messages vocaux personnalisés dans Azure, plusieurs facteurs d’authentification
1.  Créez un message vocal personnalisé à l’aide d’un des formats de fichier pris en charge.
2.  Ouvrez une session sur http://azure.microsoft.com
3.  Naviguez vers le portail de gestion AMF par les instructions en haut de cette page.
4.  Dans le portail de gestion Azure plusieurs facteurs d’authentification, cliquez sur Messages vocaux sous la section de configuration.
5.  Dans la section Messages vocaux, cliquez sur **Nouveau Message vocal**.
![Nuage](./media/multi-factor-authentication-whats-next/custom1.png)
6.  Sur la configurer : nouveaux Messages vocaux, cliquez sur **Gérer les fichiers son**.
![Nuage](./media/multi-factor-authentication-whats-next/custom2.png)
7.  Sur la configurer : page fichiers de son, cliquez sur **Télécharger le fichier audio**.
![Nuage](./media/multi-factor-authentication-whats-next/custom3.png)
8.  Sur la configurer : télécharger le fichier audio, cliquez sur **Parcourir** et naviguez jusqu'à votre message vocal, cliquez sur **Ouvrir**.
![Nuage](./media/multi-factor-authentication-whats-next/custom4.png)
9.  Ajouter une Description et cliquez sur Télécharger.
10. Une fois cette opération terminée, un message confirme que vous avez correctement téléchargé le fichier.
11. Sur la gauche, cliquez sur Messages vocaux.
12. Dans la section Messages vocaux, cliquez sur Nouveau Message vocal.
13. Dans la liste déroulante langue, sélectionnez une langue.
14. Si ce message est pour une application spécifique, spécifier dans la zone d’Application.
15. À partir du Type de Message, sélectionnez le type de message à être substituée par notre nouveau message personnalisé.
16. Dans le menu déroulant fichier audio, sélectionnez votre fichier audio.
17. Cliquez sur **créer**. Un message confirme que vous avez créé avec succès un message vocal.
![Nuage](./media/multi-factor-authentication-whats-next/custom5.png)</center>



## <a name="caching-in-azure-multi-factor-authentication"></a>La mise en cache dans une authentification à facteurs multiples Azure

La mise en cache vous permet de définir une heure spécifique de période afin que les tentatives d’authentification suivantes réussissent automatiquement. Cela est principalement utilisé lorsque les systèmes sur site VPN envoient plusieurs demandes de vérification lors de la première demande est toujours en cours. Ainsi, les demandes suivantes réussir automatiquement après que l’utilisateur a réussi la vérification en cours. Notez que la mise en cache n’est pas destiné à être utilisé pour les connexions à Active Directory Azure.


### <a name="to-set-up-caching-in-azure-multi-factor-authentication"></a>Pour configurer la mise en cache dans Azure, plusieurs facteurs d’authentification

1.  Ouvrez une session sur http://azure.microsoft.com
2.  Naviguez vers le portail de gestion AMF par les instructions en haut de cette page.
3.  Dans le portail de gestion Azure plusieurs facteurs d’authentification, cliquez sur mise en cache dans la section de configuration.
4.  Configurer la mise en cache de page en cliquant sur Nouveau Cache
5.  Sélectionnez le type de Cache et les secondes de cache. Cliquez sur Créer.

<center>![Nuage](./media/multi-factor-authentication-whats-next/cache.png)</center>

## <a name="trusted-ips"></a>Adresses IP autorisées

Approuvé Qu'ips est une fonction de plusieurs facteurs d’authentification qui permet aux administrateurs d’un client géré ou fédéré ignorer plusieurs facteurs d’authentification pour les utilisateurs qui sont la signature à partir de l’intranet local de la société. Les fonctionnalités sont disponibles pour les locataires AD Azure qui ont des licences Azure AD Premium, Suite de mobilité d’entreprise ou authentification à plusieurs facteurs Azure.


Type d’Azure AD locataire| Options IP autorisées disponibles
:------------- | :------------- |
Géré|Plages d’adresses IP spécifiques – les administrateurs peuvent spécifier une plage d’adresses IP qui peuvent contourner l’authentification à plusieurs facteurs pour les utilisateurs qui sont la signature à partir de l’intranet de la société.
Fédéré|<li>Tous les utilisateurs fédérés - tous les utilisateurs fédérés, qui sont la signature à l’aide de l’organisation contournent plusieurs facteurs d’authentification à l’aide d’une demande émise par AD FS.</li><li>Plages d’adresses IP spécifiques – les administrateurs peuvent spécifier une plage d’adresses IP qui peuvent contourner l’authentification à plusieurs facteurs pour les utilisateurs qui sont la signature à partir de l’intranet de la société.

Ce contournement ne fonctionne qu’à partir de l’intérieur de l’intranet d’une entreprise. Par exemple, si vous n’avez sélectionné tous les utilisateurs fédérés, et qu’un utilisateur se connecte à partir d’à l’extérieur de l’intranet de la société, cet utilisateur dispose pour s’authentifier à l’aide de l’authentification multicritères même si l’utilisateur présente une demande d’indemnisation AD FS. Le tableau suivant décrit quand plusieurs facteurs d’authentification et d’application les mots de passe sont requis à l’intérieur de votre réseau d’entreprise et en dehors de votre réseau d’entreprise lorsque IPs de confiance est activé.


|Adresses IP activé autorisées| Approuvé IPs désactivé
:------------- | :------------- | :------------- |
Réseau d’entreprise interne|Pour les flux de navigateur, une authentification multifacteur pas nécessaire.|Pour les flux de navigateur, plusieurs facteurs d’authentification requise
|Pour les applications client riche, des mots de passe régulières fonctionnent si l’utilisateur n’a pas créé les mots de passe d’application. Une fois qu’un mot de passe d’application a été créé, les mots de passe app sont requis.|Pour les applications client riche, les mots de passe app requis
Réseau d’entreprise à l’extérieur|Pour les flux de navigateur, plusieurs facteurs d’authentification requis.|Pour les flux de navigateur, plusieurs facteurs d’authentification requis.
|Pour les applications client riche, les mots de passe app requis.|Pour les applications client riche, les mots de passe app requis.

### <a name="to-enable-trusted-ips"></a>Pour activer la confiance des IPs

1. Ouvrez une session dans le portail classique Azure.
2. Sur la gauche, cliquez sur Active Directory.
3. Sous, répertoire cliquez sur le répertoire que vous souhaitez configurer les IPsing de confiance sur.
4. Dans le répertoire que vous avez sélectionné, cliquez sur Configurer.
5. Dans la section authentification à facteurs multiples, cliquez sur Gérer les paramètres de service.
6. Dans la page Paramètres du Service, sous confiance IPs, sélectionnez :

    - Pour demandes de fédéré les utilisateurs provenant de l’intranet – tous fédérés utilisateurs sont une session depuis le réseau de l’entreprise permet d’ignorer plusieurs facteurs d’authentification à l’aide d’une demande émise par AD FS.
    - Pour les requêtes à partir d’une plage spécifique de publics IPs – Saisissez les adresses IP dans les zones fournies à l’aide de la notation CIDR. Par exemple : xxx.xxx.xxx.0/24 pour les adresses IP dans la plage xxx.xxx.xxx.1 – xxx.xxx.xxx.254 ou xxx.xxx.xxx.xxx/32 pour une adresse IP unique. Vous pouvez entrer jusqu'à 50 plages d’adresses IP.

7. Cliquez sur Enregistrer.
8. Une fois les mises à jour ont été appliquées, cliquez sur Fermer.



![Adresses IP autorisées](./media/multi-factor-authentication-whats-next/trustedips3.png)




## <a name="app-passwords"></a>Application de mots de passe

Dans certaines applications, comme Office 2010 ou plus et un Apple Mail vous ne pouvez pas utiliser l’authentification à plusieurs facteurs.  Pour utiliser ces applications, vous devez utiliser « mots de passe app » à la place de votre mot de passe traditionnel.  Le mot de passe d’application permet à l’application à ignorer l’authentification à facteurs multiples et continuer à travailler.

>[AZURE.NOTE] Authentification des Clients Office 2013 moderne
>
> Les clients Office 2013 (y compris Outlook) prise en charge de nouveaux protocoles d’authentification maintenant et peuvent être activés pour prendre en charge d’authentification à plusieurs facteurs.  Cela signifie qu’une fois activé, les mots de passe app ne sont pas nécessaires pour une utilisation avec les clients Office 2013.  Pour plus d’informations, voir [Office 2013 d’authentification modernes public preview a annoncé](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).



### <a name="important-things-to-know-about-app-passwords"></a>Informations importantes à connaître à propos des mots de passe app

Voici la liste des choses que vous devez savoir sur les mots de passe app importante.

- Les utilisateurs peuvent avoir plusieurs mots de passe des applications, qui augmente la surface d’exposition pour le vol. Les mots de passe app étant difficiles à retenir, elle peut encourager les utilisateurs à noter ce qui suit. Cela n’est pas recommandée et doit être déconseillée car seul facteur est requis pour vous connecter avec le mot de passe app.
- Les applications qui mettent en cache les mots de passe et l’utilisent dans les scénarios locaux peuvent démarrer échoue car le mot de passe d’application n’est pas connue à l’extérieur de l’id d’organisation. Par exemple, les e-mails Exchange qui sont sur site, mais les messages archivés sont dans le nuage. Le mot de passe ne fonctionne pas.
- Le mot de passe est généré automatiquement et n’est pas fourni par l’utilisateur. En effet, le mot de passe généré automatiquement, est plus difficile pour un intrus de deviner et est plus sécurisée.
- Actuellement il existe une limite de 40 mots de passe par utilisateur. Vous devrez supprimer l’un de vos mots de passe d’application existant pour créer un nouveau.
- Une fois que plusieurs facteurs d’authentification est activé sur un compte d’utilisateur, mots de passe app peuvent être utilisés avec la plupart des autres que des navigateurs clients tels qu’Outlook et Lync, mais les actions d’administration ne peut pas être effectuées à l’aide de mots de passe app grâce à des applications autres que des navigateurs tels que Windows PowerShell même si cet utilisateur a un compte d’administrateur.  Assurez-vous que vous créez un compte de service avec un mot de passe fort pour exécuter des scripts PowerShell et que vous n’activez pas ce compte pour l’authentification à plusieurs facteurs.

>[AZURE.WARNING]  Les mots de passe App ne fonctionnent pas dans les environnements hybrides où les clients communiquent avec à la fois sur site et nuage de points de terminaison de service de découverte automatique. C’est parce que les mots de passe de domaine sont nécessaires pour authentifier sur site et les mots de passe app doivent s’authentifier avec le nuage.


### <a name="naming-guidance-for-app-passwords"></a>Instructions d’affectation de noms pour l’application de mots de passe
Il est recommandé que les noms de mot de passe d’application doivent refléter le périphérique sur lequel ils sont utilisés. Par exemple, si vous avez un ordinateur portable qui a des applications autres que des navigateurs tels que Outlook, Word et Excel, vous devez uniquement créer un mot de passe application nommée d’un ordinateur portable et l’utilisation de ce mot de passe d’application dans l’ensemble de ces applications. Bien que vous pouvez créer des mots de passe distincts pour l’ensemble de ces applications, il n’est pas recommandé. La recommandation méthode consiste à utiliser un mot de passe app par périphérique.


<center>![Nuage](./media/multi-factor-authentication-whats-next/naming.png)</center>


### <a name="federated-sso-app-passwords"></a>Mots de passe App fédéré (SSO)
Annonce Azure prend en charge la fédération avec local Windows Server Active Directory Domain Services (AD DS). Si votre organisation est federated(SSO) avec AD Azure et vous allez être à l’aide de l’authentification multifactorielle Azure, puis Voici les informations importantes que vous devez être conscient lors de l’utilisation de mots de passe app. Cela s’applique uniquement aux clients de federated(SSO).

- Le mot de passe d’application est vérifiée par AD Azure et ignore donc de fédération. Fédération sert uniquement activement lorsque vous configurez l’application de mot de passe.
- Pour les utilisateurs de federated(SSO), nous atteindre jamais le fournisseur d’identité (IdP) à la différence du flux passif. Les mots de passe sont stockés dans l’id d’organisation. Si l’utilisateur quitte la société, cette information est à l’id d’organisation à l’aide de la synchronisation d’annuaire en temps réel. Désactivation/suppression du compte peut prendre jusqu'à trois heures pour la synchronisation, de retarder la désactivation/suppression du mot de passe de l’application dans Azure AD.
- Paramètres de contrôle d’accès au Client local ne sont pas respectés par application de mot de passe
- Aucune authentification locales consignation / audit de fonctionnalité n’est disponible pour l’application de mot de passe
- Éducation de l’utilisateur final plus est requise pour le client Microsoft Lync 2013. Pour les étapes à suivre, consultez Comment modifier le mot de passe de votre messagerie pour le mot de passe d’application.
- Certaines conceptions architecturales avancées peuvent nécessiter l’utilisation d’une combinaison de nom d’utilisateur d’organisation et les mots de passe et les mots de passe app lors de l’utilisation de plusieurs facteurs d’authentification avec les clients, en fonction de l’endroit où ils s’authentifient. Pour les clients qui s’authentifient par rapport à une infrastructure sur site, vous utilisez un nom d’utilisateur d’organisation et d’un mot de passe. Pour les clients qui s’authentifient AD Azure, vous devez utiliser le mot de passe d’application.

Par exemple, supposons que vous disposez d’une architecture qui comprend les éléments suivants :

- Vous vous fédérez votre instance sur site d’Active Directory avec Azure AD
- Vous utilisez Exchange en ligne
- Vous utilisez Lync qui est spécifiquement sur site
- Vous utilisez une authentification à facteurs multiples Azure


![Proofup](./media/multi-factor-authentication-whats-next/federated.png)

 Dans ces cas, vous devez effectuer les opérations suivantes :

- Lors de la signature dans Lync, utilisez le nom d’utilisateur et mot de passe de votre entreprise.
- Lorsque vous tentez d’accéder au carnet de commandes via un client Outlook qui se connecte à Exchange en ligne, utilisez un mot de passe d’application.

### <a name="allowing-app-password-creation"></a>Autorisation de création de mot de passe d’application
Par défaut, les utilisateurs ne peuvent pas créer des mots de passe app.  Cette fonctionnalité doit être activée.  Pour permettre aux utilisateurs la possibilité de créer des mots de passe d’application, utilisez la procédure suivante.

#### <a name="to-enable-users-to-create-app-passwords"></a>Pour permettre aux utilisateurs de créer des mots de passe app



1. Connectez-vous au portail Azure classique.
2. Sur la gauche, cliquez sur Active Directory.
3. Sous, répertoire cliquez sur le répertoire de l’utilisateur que vous souhaitez activer.
4. En haut, cliquez sur utilisateurs.
5. En bas de la page, cliquez sur Gérer l’AUTH multi-facteurs.  
6. En haut de la page d’authentification à facteurs multiples, cliquez sur Paramètres du Service.
7. Assurez-vous que la case Autoriser les utilisateurs à créer des mots de passe app pour signer dans des applications autres que des navigateurs est sélectionnée.


![Créer des mots de passe App](./media/multi-factor-authentication-whats-next/trustedips3.png)

### <a name="creating-app-passwords"></a>Création de mots de passe app
Les utilisateurs peuvent créer des mots de passe app lors de leur enregistrement initial.  Ils ont la possibilité à la fin du processus d’inscription qui leur permet de les créer.

En outre utilisateurs peuvent également créer les mots de passe app ultérieurement en modifiant les paramètres dans le portail d’Azure, le portail Office 365 ou en

### <a name="to-create-app-passwords-in-the-office-365-portal"></a>Pour créer des mots de passe app dans le portail Office 365
--------------------------------------------------------------------------------


1. Connectez-vous au portail Office 365
2. Dans le coin supérieur droit, sélectionnez le widget de paramètres
3. Sur la gauche, sélectionnez la vérification de sécurité supplémentaires
4. Sur la droite, sélectionnez **mise à jour de mes numéros de téléphone utilisés pour la sécurité des comptes**
5. Sur la page proofup, en haut, sélectionnez les mots de passe app
6. Cliquez sur **créer**
7. Entrez un nom pour le mot de passe d’application et cliquez sur **suivant**
8. Copier le mot de passe d’application dans le Presse-papiers et les coller dans votre application.

<center>![Nuage](./media/multi-factor-authentication-whats-next/security.png)</center>


### <a name="to-create-app-passwords-in-the-azure-portal"></a>Pour créer des mots de passe application dans Azure portal
--------------------------------------------------------------------------------
1. Connectez-vous au portail Azure classique.
3. En haut, avec le bouton droit sur votre nom d’utilisateur, puis sélectionnez Vérification de sécurité supplémentaire.
5. Sur la page proofup, en haut, sélectionnez les mots de passe app
6. Cliquez sur **créer**
7. Entrez un nom pour le mot de passe d’application et cliquez sur **suivant**
8. Copier le mot de passe d’application dans le Presse-papiers et les coller dans votre application.


![Les mots de passe App](./media/multi-factor-authentication-whats-next/app2.png)

### <a name="to-create-app-passwords-if-you-do-not-have-an-office-365-or-azure-subscription"></a>Pour créer des mots de passe app si vous ne disposez pas d’un abonnement à Office 365 ou Azure
--------------------------------------------------------------------------------
1. Se connecter à [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Dans la partie supérieure, sélectionnez le profil.
3. Cliquez sur votre nom d’utilisateur, sélectionnez la vérification de sécurité supplémentaires.
5. Sur la page proofup, en haut, sélectionnez les mots de passe app
6. Cliquez sur **créer**
7. Entrez un nom pour le mot de passe d’application et cliquez sur **suivant**
8. Copier le mot de passe d’application dans le Presse-papiers et les coller dans votre application.

![Les mots de passe App](./media/multi-factor-authentication-whats-next/myapp.png)

## <a name="remember-multi-factor-authentication-for-devices-users-trust"></a>N’oubliez pas d’authentification à plusieurs facteurs pour la confiance des utilisateurs de périphériques

Mémoriser plusieurs facteurs d’authentification pour les périphériques et navigateurs que confiance des utilisateurs est une fonctionnalité disponible pour tous les utilisateurs de l’AMF.  Il vous permet de donner aux utilisateurs la possibilité de dérivation AMF pendant un nombre défini de jours après l’exécution d’une connexion à l’aide d’AMF. Ceci permet d’améliorer la facilité d’utilisation pour vos utilisateurs.

Toutefois, dans la mesure où les utilisateurs sont autorisés à retenir AMF pour périphériques approuvés, cette fonctionnalité peut réduire la sécurité des comptes. Pour garantir la sécurité des comptes, vous devez restaurer une authentification multifactorielle pour leurs périphériques pour une ou l’autre des scénarios suivants :

- Si leur compte d’entreprise est plus sécurisé.
- Si un dispositif à retenir est perdu ou volé

> [AZURE.NOTE] Cette fonction est implémentée sous la forme d’un cache de cookie du navigateur. Il ne fonctionne pas si les cookies de votre navigateur ne sont pas activés.

### <a name="how-to-enabledisable-remember-multi-factor-authentication"></a>Comment faire pour activer/désactiver mémoriser plusieurs facteurs d’authentification

1. Connectez-vous au portail Azure classique.
2. Sur la gauche, cliquez sur Active Directory.
3. Dans Active Directory, cliquez sur le répertoire que vous souhaitez configurer n’oubliez pas que l’authentification à facteurs multiples pour les périphériques.
4. Dans le répertoire que vous avez sélectionné, cliquez sur Configurer.
5. Dans la section authentification à facteurs multiples, cliquez sur Gérer les paramètres de service.
6. Dans la page Paramètres du Service, sous de gérer les paramètres utilisateur de l’appareil, de sélectionner/désélectionner les **Autoriser les utilisateurs à se souvenir de plusieurs facteurs d’authentification sur les périphériques qu’ils approuvent**.
![N’oubliez pas de périphériques](./media/multi-factor-authentication-whats-next/remember.png)
8. Définir le nombre de jours pendant lesquels vous souhaitez autoriser la suspension. La valeur par défaut est de 14 jours.
9. Cliquez sur Enregistrer.
10. Cliquez sur Fermer.


## <a name="selectable-verification-methods"></a>Méthodes de vérification sélectionnables
Sur les versions le nuage et sur site, vous pouvez choisir les méthodes de vérification sont disponibles pour vos utilisateurs. Le tableau ci-dessous fournit un bref aperçu de chaque méthode.

Lorsque les utilisateurs s’inscrire leurs comptes de l’AMF, ils choisissent leur méthode de vérification par défaut sur les options que vous avez activés. Le Guide pour leur processus d’inscription est couvert dans [configurer mon compte pour la vérification des deux étapes](multi-factor-authentication-end-user-first-time.md)

Méthode|Description
:------------- | :------------- |
Appelez le numéro de téléphone |  Place un appel automatisé de messages vocaux sur le téléphone de l’authentification. L’utilisateur répond à l’appel et appuie sur # dans le clavier de téléphone pour l’authentification. Ce numéro de téléphone n’est pas synchronisé sur les sites Active Directory.
Message de texte sur le téléphone | Envoie un message texte contenant un code de vérification à l’utilisateur. L’utilisateur est invité à une réponse au message texte avec le code de vérification ou d’entrer le code de vérification dans l’interface utilisateur.
Notification par l’application mobile | Dans ce mode, l’application Microsoft Authenticator empêche l’accès non autorisé aux comptes et s’arrête à des transactions frauduleuses. Pour cela, à l’aide d’une notification de transmission à votre téléphone ou votre périphérique enregistré. Afficher simplement la notification, et il est légitime que vous cliquez sur vérifier. Dans le cas contraire, vous pouvez choisir de refuser ou choisir de refuser et de signaler la notification frauduleuse. Pour plus d’informations sur la déclaration des notifications frauduleuses, voir comment utiliser la fonctionnalité de rapport de fraude et Deny pour authentification à plusieurs facteurs.</br></br>L’application Microsoft Authenticator est disponible pour [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072)et [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).|
Code de vérification à partir d’une application mobile | Dans ce mode, l’application Microsoft Authenticator peut servir un jeton logiciel pour générer un code de vérification serment. Ce code de vérification peut ensuite être entré ainsi que le nom d’utilisateur et le mot de passe à fournir la deuxième forme d’authentification.</li><br><p> L’application Microsoft Authenticator est disponible pour [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072)et [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

### <a name="how-to-enabledisable-authentication-methods"></a>Comment faire pour activer/désactiver les méthodes d’authentification

1. Connectez-vous au portail Azure classique.
2. Sur la gauche, cliquez sur Active Directory.
3. Dans Active Directory, cliquez sur le répertoire que vous souhaitez activer ou désactiver les méthodes d’authentification.
4. Dans le répertoire que vous avez sélectionné, cliquez sur Configurer.
5. Dans la section authentification à facteurs multiples, cliquez sur Gérer les paramètres de service.
6. Dans la page Paramètres du Service, sous options de vérification, de sélectionner/désélectionner les options que vous souhaitez utiliser.</br></br>
![Options de vérification](./media/multi-factor-authentication-whats-next/authmethods.png)
9. Cliquez sur Enregistrer.
10. Cliquez sur Fermer.
