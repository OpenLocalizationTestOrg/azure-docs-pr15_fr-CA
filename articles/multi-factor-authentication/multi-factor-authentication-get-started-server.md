<properties 
    pageTitle="Mise en route avec le serveur de l’authentification multifactorielle Azure"
    description="Il s’agit de la page d’authentification Azure plusieurs facteurs qui décrit la procédure de mise en route avec Azure AMF Server."
    services="multi-factor-authentication"
    keywords="l’authentification serveur, azure multi facteur d’authentification app page activation, téléchargement du serveur d’authentification"
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

# <a name="getting-started-with-the-azure-multi-factor-authentication-server"></a>Mise en route avec le serveur de l’authentification multifactorielle Azure




<center>![Nuage](./media/multi-factor-authentication-get-started-server/server2.png)</center>

Maintenant que nous avons déterminé si à utiliser sur site plusieurs facteurs d’authentification, nous allons apprendre continu. Cette page présente une nouvelle installation du serveur et de l’obtention de programme d’installation sur site Active Directory. Si vous déjà PhoneFactor server êtes installé et cherchez à mettre à niveau, consultez [mise à niveau vers le serveur de plusieurs facteurs Azure](multi-factor-authentication-get-started-server-upgrade.md) ou si vous recherchez des informations sur l’installation de simplement le service web voir [déployer le Service Web de Azure plusieurs facteurs d’authentification serveur Mobile App](multi-factor-authentication-get-started-server-webservice.md).


## <a name="download-the-azure-multi-factor-authentication-server"></a>Télécharger le serveur d’authentification à plusieurs facteurs Azure



Il existe deux méthodes différentes que vous pouvez télécharger le serveur de l’authentification multifactorielle Azure. Les deux sont effectuées via le portail Azure. Le premier est en gérant le fournisseur d’authentification selon plusieurs facteurs directement. La seconde est via les paramètres du service. La seconde option nécessite soit une licence Azure AMF, Azure AD Premium ou Suite de mobilité d’entreprise ou un fournisseur d’authentification selon plusieurs facteurs.


### <a name="to-download-the-azure-multi-factor-authentication-server-from-the-azure-portal"></a>Pour télécharger le serveur d’authentification à plusieurs facteurs Azure à partir du portail Azure
--------------------------------------------------------------------------------

1. Connectez-vous au portail Azure en tant qu’administrateur.
2. Sur la gauche, sélectionnez Active Directory.
3. Dans la page Active Directory, en haut, cliquez sur **Fournisseurs d’authentification selon plusieurs facteurs**
4. Cliquez sur **Gérer** dans le bas.
5. Une nouvelle page s’ouvre.  Cliquez sur **téléchargement.** 
 ![Télécharger](./media/multi-factor-authentication-sdk/download.png)
6. Au-dessus **Générer les informations d’identification de l’Activation**, cliquez sur **téléchargement.** 
 ![Télécharger](./media/multi-factor-authentication-get-started-server/download4.png)
7. Enregistrer le fichier téléchargé.



### <a name="to-download-the-azure-multi-factor-authentication-server-via-the-service-settings"></a>Pour télécharger le serveur d’authentification selon plusieurs facteurs Azure via les paramètres de service


1. Connectez-vous au portail Azure en tant qu’administrateur.
2. Sur la gauche, sélectionnez Active Directory.
3. Double cliquez sur votre instance de publicité Azure.
4. Dans la partie supérieure, cliquez sur **configurer**
![télécharger](./media/multi-factor-authentication-sdk/download2.png)
5. Sous authentification à facteurs multiples, sélectionnez **Gérer les paramètres de service**
6. Dans la page Paramètres de services, en bas de l’écran, cliquez sur **Atteindre le portail**.
![Télécharger](./media/multi-factor-authentication-get-started-server/servicesettings.png)
7. Une nouvelle page s’ouvre. Cliquez sur **téléchargement.**
8. Au-dessus **Générer les informations d’identification de l’Activation**, cliquez sur **téléchargement.**
9. Enregistrer le fichier téléchargé.




## <a name="install-and-configure-the-azure-multi-factor-authentication-server"></a>Installer et configurer le serveur d’authentification à plusieurs facteurs Azure
Maintenant que vous avez téléchargé le serveur, vous pouvez installer et le configurer.  Assurez-vous que le serveur sur que vous l’installez remplit les conditions suivantes :



Configuration requise du serveur Azure plusieurs facteurs d’authentification|Description|
:------------- | :------------- |
Matériel|<li>200 Mo d’espace disque</li><li>processeur capable de x32 ou de x64</li><li>1 Go de RAM ou plus</li>
Logiciel|<li>Windows Server 2008 ou supérieur si l’hôte est une système d’exploitation de serveur</li><li>Windows 7 ou version ultérieure, si l’hôte est un système d’exploitation client</li><li>4.0 de Microsoft .NET Framework</li><li>Kit de développement IIS version 7.0 ou ultérieure, si l’installation du service de portail ou le web utilisateur</li>

### <a name="azure-multi-factor-authentication-server-firewall-requirements"></a>Exigences en matière de pare-feu plusieurs facteurs d’authentification serveur Azure
--------------------------------------------------------------------------------
Chaque serveur de l’AMF doit être en mesure de communiquer sur le port 443 sortant à ce qui suit :

- https://PFD.phonefactor.NET
- https://pfd2.phonefactor.NET
- https://CSS.phonefactor.NET

Si le pare-feu sortants sont limités sur le port 443, les plages d’adresses IP suivantes seront doivent être ouverts :

Sous-réseau IP|Masque de réseau|Plage d’adresses IP
:------------- | :------------- | :------------- |
134.170.116.0/25|255.255.255.128|134.170.116.1 – 134.170.116.126
134.170.165.0/25|255.255.255.128|134.170.165.1 – 134.170.165.126
70.37.154.128/25|255.255.255.128|70.37.154.129 – 70.37.154.254

Si vous n’utilisez pas les fonctionnalités de Confirmation d’événement Azure plusieurs facteurs d’authentification et si les utilisateurs ne sont pas authentifier avec l’authentification de plusieurs facteurs les applications mobiles à partir de périphériques sur le réseau d’entreprise, l’adresse IP des plages peuvent être réduits à ce qui suit :


Sous-réseau IP|Masque de réseau|Plage d’adresses IP
:------------- | :------------- | :------------- |
134.170.116.72/29|255.255.255.248|134.170.116.72 – 134.170.116.79
134.170.165.72/29|255.255.255.248|134.170.165.72 – 134.170.165.79
70.37.154.200/29|255.255.255.248|70.37.154.201 – 70.37.154.206


### <a name="to-install-and-configure-the-azure-multi-factor-authentication-server"></a>Pour installer et configurer le serveur d’authentification à plusieurs facteurs Azure
--------------------------------------------------------------------------------


1. Double-cliquez sur le fichier exécutable. Cela commence l’installation.
2. Dans l’écran Sélectionner le dossier, assurez-vous que le dossier est correct, puis cliquez sur Suivant.
3. Une fois l’installation terminée, cliquez sur Terminer.  Cette action lance l’Assistant de configuration.
4. Dans l’Assistant de configuration écran de bienvenue, activez **Ignorer à l’aide de l’Assistant de Configuration de l’authentification** et cliquez sur **suivant**.  Ceci va fermer l’Assistant et démarrez le serveur.
    ![Nuage](./media/multi-factor-authentication-get-started-server/skip2.png)
5. Sur la page que nous avons téléchargé du serveur à partir de, cliquez sur le bouton **Générer les informations d’identification de l’Activation** .  Copie de ces informations au serveur d’AMF Azure dans les zones appropriées et cliquez sur **Activer**.


Les étapes ci-dessus montrent une installation rapide avec l’Assistant de configuration.  Vous pouvez réexécuter l’Assistant l’authentification en la sélectionnant dans le menu Outils sur le serveur.



##<a name="import-users-from-active-directory"></a>Importation d’utilisateurs à partir d’Active Directory

Maintenant que le serveur est installé et configuré, vous pouvez importer rapidement les utilisateurs sur le serveur d’AMF Azure.

### <a name="to-import-users-from-active-directory"></a>Pour importer des utilisateurs à partir d’Active Directory
--------------------------------------------------------------------------------


1. Dans le serveur AMF Azure, sur la gauche, sélectionnez **les utilisateurs**.
2. Dans le bas, sélectionnez **Importer à partir d’Active Directory**.
3. Maintenant vous pouvez rechercher des utilisateurs individuels ou recherche dans l’annuaire Active Directory pour les unités d’organisation avec des utilisateurs dans les.  Dans ce cas, nous Spécifie l’unité d’organisation utilisateurs.
4. Mettez en surbrillance tous les utilisateurs sur la droite, puis cliquez sur **Importer**.  Vous recevrez un message vous indiquant que vous avez réussi.  Fermez la fenêtre d’importation.

![Nuage](./media/multi-factor-authentication-get-started-server/import2.png)

## <a name="send-users-an-email"></a>Envoyer aux utilisateurs un message électronique
Maintenant que vous avez importé vos utilisateurs sur le serveur d’authentification à plusieurs facteurs Azure, il est recommandé que vous envoyez à vos utilisateurs un e-mail les informant qu’ils ont été inscrits dans une authentification à plusieurs facteurs.

Avec le serveur de l’authentification multifactorielle Azure, il existe différentes façons de configurer les utilisateurs pour utiliser une authentification multifacteur.  Par exemple, si vous connaissez les numéros de téléphone de l’utilisateur ont été en mesure d’importer les numéros de téléphone serveur Azure plusieurs facteurs d’authentification à partir du répertoire de leur société, le courrier électronique permet aux utilisateurs de savoir qu’ils ont été configurés pour utiliser l’authentification selon plusieurs facteurs Azure, fournissent des instructions sur l’utilisation de l’authentification multifactorielle Azure et informer l’utilisateur du numéro de téléphone, sur qu'ils recevront leurs authentifications.  

Le contenu du message peut varier selon la méthode d’authentification qui a été définie pour l’utilisateur (par exemple, téléphone, SMS, application mobile).  Par exemple, si l’utilisateur est tenu d’utiliser un code PIN lors de l’authentification, l’e-mail les indiquera que leur code PIN initial a été défini.  Généralement, les utilisateurs doivent modifier leur code PIN lors de leur première authentification.

Si les numéros de téléphone des utilisateurs n’ont pas été configurés ou importés dans le serveur de l’authentification multifactorielle Azure, ou si les utilisateurs sont préconfigurés de manière à utiliser l’application mobile pour l’authentification, vous pouvez leur envoyer un message qui les informe qu’ils ont été configurés pour utiliser l’authentification selon plusieurs facteurs Azure et dirigera les pour terminer l’inscription de leur compte via le portail Azure plusieurs facteurs d’authentification utilisateur.  Un lien hypertexte à inclus que l’utilisateur clique sur pour accéder au portail de l’utilisateur. Lorsque l’utilisateur clique sur le lien hypertexte, leur navigateur web s’ouvre et copiez-les sur le portail de leur société Azure plusieurs facteurs d’authentification utilisateur.   


### <a name="configuring-email-and-email-templates"></a>Configuration du courrier électronique et des modèles d’e-mail

En cliquant sur l’icône d’e-mail située à gauche, vous pouvez configurer les paramètres pour l’envoi de ces messages électroniques.  Voici où vous pouvez entrer des informations de votre serveur de messagerie SMTP et il vous permet d’envoyer un e-mail de large ouverte en ajoutant un contrôle pour envoyer les courriers à la case à cocher utilisateurs.

![Paramètres de messagerie](./media/multi-factor-authentication-get-started-server/email1.png)

Sous l’onglet contenu de courrier électronique, vous verrez tous les différents modèles d’e-mail sont disponibles.  En fonction de comment vous avez configuré vos utilisateurs pour utiliser une authentification multifacteur, vous pouvez sélectionner le modèle qui convient le mieux à vous.

![Modèles de courriel](./media/multi-factor-authentication-get-started-server/email2.png)

## <a name="how-the-azure-multi-factor-authentication-server-handles-user-data"></a>Comment le serveur de l’authentification multifactorielle Azure gère les données de l’utilisateur

Lorsque vous utilisez le serveur de l’authentification multifactorielle (AMF) sur site, les données d’un utilisateur sont stockées sur les serveurs locaux. Aucune donnée utilisateur permanentes n’est stockée dans le nuage. Lorsque l’utilisateur effectue une authentification à deux facteurs, le serveur de l’AMF envoie les données au service de cloud Azure AMF pour effectuer l’authentification. Lorsque ces demandes d’authentification sont envoyés vers le service en nuage, les champs suivants sont envoyés dans la demande et des journaux pour qu’ils soient disponibles dans les rapports de l’utilisation d’authentification du client. Certains champs sont facultatifs et peuvent être activées ou désactivées dans le serveur d’authentification selon plusieurs facteurs. La communication entre le serveur de l’AMF au service cloud AMF utilise SSL/TLS sur le port 443 sortant. Ces champs sont les suivants :

- ID unique - nom d’utilisateur ou de l’ID de serveur interne AMF
- Premier et dernier nom - facultatif
- Adresse e-mail - facultatif
- Numéro de téléphone - lors d’un appel vocal ou une authentification de SMS
- Jeton de périphérique - lors d’une authentification de l’application mobile
- Mode d’authentification
- Résultat de l’authentification
- Nom du serveur AMF
- Serveur de l’AMF IP
- Client IP – si disponible



Outre les champs ci-dessus, le résultat de l’authentification (réussite/refus) et de la raison de tout refus d’accès est stockée avec les données d’authentification et disponibles via les rapports de l’utilisation d’authentification.


## <a name="advanced-azure-multi-factor-authentication-server-configurations"></a>Les Configurations de serveur Azure plusieurs facteurs d’authentification avancées
Pour plus d’informations sur le programme d’installation avancé et les informations de configuration, utilisez le tableau ci-dessous.

Méthode|Description
:------------- | :------------- |
[Portail de l’utilisateur](multi-factor-authentication-get-started-portal.md)|  Informations sur le programme d’installation et de configuration du portail utilisateur, y compris les utilisateurs libre-service et déploiement.
[Service de fédération Active Directory](multi-factor-authentication-get-started-adfs.md)|Informations sur la configuration d’une authentification à facteurs multiples Azure avec AD FS.
[Authentification RADIUS](multi-factor-authentication-get-started-server-radius.md)|  Informations sur le programme d’installation et de la configuration du serveur AMF Azure avec RADIUS.
[Authentification IIS](multi-factor-authentication-get-started-server-iis.md)|Informations sur le programme d’installation et de la configuration du serveur AMF Azure avec IIS.
[Authentification Windows](multi-factor-authentication-get-started-server-windows.md)|  Informations sur le programme d’installation et de la configuration du serveur d’AMF Azure avec l’authentification Windows.
[Authentification LDAP](multi-factor-authentication-get-started-server-ldap.md)|Informations sur le programme d’installation et de la configuration du serveur AMF Azure avec l’authentification LDAP.
[Passerelle des services Bureau à distance et Azure plusieurs facteurs d’authentification serveur, à l’aide de RADIUS](multi-factor-authentication-get-started-server-rdg.md)|  Informations sur le programme d’installation et de la configuration du serveur AMF Azure avec la passerelle des services Bureau à distance à l’aide de RADIUS.
[Synchronisation avec Active Directory de Windows Server](multi-factor-authentication-get-started-server-dirint.md)|Informations sur le programme d’installation et de configuration de la synchronisation entre Active Directory et le serveur AMF Azure.
[Déploiement du Service de Mobile application Web Server Azure plusieurs facteurs d’authentification](multi-factor-authentication-get-started-server-webservice.md)|Informations sur le programme d’installation et de configuration du service web du serveur Azure AMF.
