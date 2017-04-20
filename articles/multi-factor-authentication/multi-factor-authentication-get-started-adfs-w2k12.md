<properties
    pageTitle="Serveur AMF avec Windows Server 2012 R2 AD FS | Microsoft Azure"
    description="Cet article décrit comment démarrer avec authentification à plusieurs facteurs Azure et AD FS dans Windows Server 2012 R2."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/14/2016"
    ms.author="kgremban"/>


# <a name="secure-your-cloud-and-on-premises-resources-using-azure-multi-factor-authentication-server-with-ad-fs-in-windows-server-2012-r2"></a>Protéger vos ressources de cloud et sur site à l’aide du serveur de l’authentification multifactorielle Azure à AD FS dans Windows Server 2012 R2

Si vous utilisez Active Directory Federation Services (ADFS) et que vous souhaitez cloud sécurisé ou les ressources sur site, vous pouvez configurer le serveur d’authentification selon plusieurs facteurs Azure fonctionne avec AD FS. Cette configuration déclenche la vérification en deux étapes pour les points de terminaison de grande valeur.

Dans cet article, nous abordons à l’aide du serveur de l’authentification multifactorielle Azure à AD FS dans Windows Server 2012 R2. Pour plus d’informations, lisez Comment [sécuriser des ressources de cloud et sur site à l’aide du serveur de l’authentification multifactorielle Azure avec AD FS 2.0](multi-factor-authentication-get-started-adfs-adfs2.md).

## <a name="secure-windows-server-2012-r2-ad-fs-with-azure-multi-factor-authentication-server"></a>Sécuriser Windows Server 2012 R2 AD FS avec le serveur d’authentification à plusieurs facteurs Azure

Lorsque vous installez le serveur de l’authentification multifactorielle Azure, vous disposez des options suivantes :

- Installer le serveur de l’authentification multifactorielle Azure localement sur le même serveur que AD FS
- Installez la carte d’authentification à plusieurs facteurs Azure localement sur le serveur AD FS, puis plusieurs facteurs d’authentification serveur sur un autre ordinateur

Avant de commencer, gardez à l’esprit les informations suivantes :

- Vous n’êtes pas obligé de l’installer le serveur de l’authentification multifactorielle Azure sur votre serveur ADFS. Toutefois, vous devez installer la carte d’authentification à plusieurs facteurs pour AD FS dans Windows Server 2012 R2 qui exécute AD FS. Si elle est une version prise en charge et que vous installez la carte AD FS séparément sur votre serveur de fédération AD FS, vous pouvez installer le serveur sur un autre ordinateur. Consultez les procédures suivantes pour savoir comment installer la carte séparément.

- Lorsque la carte de l’AMF serveur AD FS a été conçue, il a été prévu que AD FS peut passer le nom de la partie de confiance à la carte. Ensuite, le nom de la partie utilisatrice peut servir en tant que nom de l’application. Toutefois, ce ne fut ne pas à être le cas. Si votre organisation utilise message texte ou méthodes de vérification d’application mobile, les chaînes définies dans les paramètres de la société contient un espace réservé, le <*nom de l’application*de $ $>. Cet espace réservé n’est pas remplacé automatiquement lorsque vous utilisez l’adaptateur AD FS. Nous vous conseillons de supprimer l’espace réservé à partir des chaînes appropriées lorsque vous sécurisez AD FS.

- Le compte que vous utilisez pour vous connecter doit avoir les droits d’utilisateur pour créer des groupes de sécurité dans le service Active Directory.

- L’Assistant installation de carte plusieurs facteurs d’authentification AD FS crée un groupe de sécurité appelé PhoneFactor Admins de votre instance d’Active Directory. Puis, il ajoute le compte de service AD FS de votre service de fédération à ce groupe. Nous vous recommandons de vérifier sur votre contrôleur de domaine que le groupe PhoneFactor Admins est effectivement créé et que le compte de service l’AD FS est un membre de ce groupe. Si nécessaire, vous devez manuellement ajouter le compte de service AD FS au groupe PhoneFactor Admins sur votre contrôleur de domaine.

- Pour plus d’informations sur l’installation du Kit de développement de Service Web avec le portail de l’utilisateur, en savoir plus sur [déploiement du portail de l’utilisateur pour le serveur de l’authentification multifactorielle Azure.](multi-factor-authentication-get-started-portal.md)


### <a name="install-azure-multi-factor-authentication-server-locally-on-the-ad-fs-server"></a>Installer le serveur de l’authentification multifactorielle Azure localement sur le serveur AD FS

1. Téléchargez et installez le serveur de l’authentification multifactorielle Azure sur votre serveur ADFS. Pour des informations sur l’installation, en savoir plus sur la [mise en route avec Azure plusieurs facteurs d’authentification serveur](multi-factor-authentication-get-started-server.md).
2. Dans la console Gestion de serveur de l’authentification multifactorielle Azure, cliquez sur l’icône **AD FS** , puis sélectionnez les options **Autoriser l’inscription utilisateur** et **Autoriser les utilisateurs à sélectionner la méthode**.
3. Sélectionnez les options supplémentaires que vous souhaitez spécifier pour votre organisation.
4. Cliquez sur **installer AD FS carte**.
<center>![Nuage](./media/multi-factor-authentication-get-started-adfs-w2k12/server.png)</center>
5. Si la fenêtre Active Directory s’affiche, ce qui signifie deux choses. Votre ordinateur est joint à un domaine, et la configuration d’Active Directory pour sécuriser la communication entre la carte d’ADFS et du service d’authentification à plusieurs facteurs est incomplète. Cliquez sur **suivant** pour terminer cette configuration automatiquement, ou sélectionner le **Ignorer la configuration automatique d’Active Directory et configurer les paramètres manuellement** case à cocher, puis cliquez sur **suivant**.
6. Si les fenêtres de groupe Local est affiché, cela signifie deux choses. Votre ordinateur n’est pas joint à un domaine, et la configuration de groupe locale pour sécuriser les communications entre la carte d’ADFS et du service d’authentification à plusieurs facteurs est incomplète. Cliquez sur **suivant** pour terminer cette configuration automatiquement, ou sélectionner le **Ignorer la configuration automatique de groupe locale et configurer les paramètres manuellement** case à cocher, puis cliquez sur **suivant**.
7. Dans l’Assistant installation, cliquez sur **suivant**. Serveur d’authentification selon plusieurs facteurs Azure crée le groupe PhoneFactor Admins et ajoute le compte de service AD FS pour le groupe PhoneFactor Admins.
<center>![Nuage](./media/multi-factor-authentication-get-started-adfs-w2k12/adapter.png)</center>
8. Sur la page **De lancement de programme d’installation** , cliquez sur **suivant**.
9. Dans le programme d’installation de la carte d’authentification à plusieurs facteurs AD FS, cliquez sur **suivant**.
10. Lorsque l’installation est terminée, cliquez sur **Fermer** .
11. Lorsque la carte a été installée, vous devez l’inscrire avec AD FS. Ouvrir Windows PowerShell et exécutez la commande suivante :<br>
    `C:\Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1`
   <center>![Nuage](./media/multi-factor-authentication-get-started-adfs-w2k12/pshell.png)</center>
12. Pour utiliser votre adaptateur nouvellement enregistré, modifier la stratégie globale de l’authentification dans AD FS. Dans la console de gestion AD FS, accédez au noeud **Stratégies d’authentification** . Dans la section **Authentification à facteurs multiples** , cliquez sur le lien **Modifier** situé en regard de la section de **Paramètres globaux** . Dans la fenêtre **Modifier la stratégie globale d’authentification** , sélectionnez **Authentification à plusieurs facteurs** comme méthode d’authentification supplémentaires, puis cliquez sur **OK**. La carte est enregistrée en tant que WindowsAzureMultiFactorAuthentication. Redémarrez le service AD FS pour l’inscription prennent effet.

<center>![Nuage](./media/multi-factor-authentication-get-started-adfs-w2k12/global.png)</center>

À ce stade, plusieurs facteurs d’authentification serveur est configuré pour être un fournisseur d’authentification supplémentaires à utiliser avec AD FS.

## <a name="install-a-standalone-instance-of-the-ad-fs-adapter-by-using-the-web-service-sdk"></a>Installez une instance autonome de l’adaptateur AD FS à l’aide du Kit de développement de Service Web
1. Installez le Kit de développement de Service Web sur le serveur qui exécute le serveur de l’authentification multifactorielle.
2. Copiez les fichiers suivants à partir de la \Program Files\Multi-répertoire de serveur de facteur d’authentification au serveur sur lequel vous envisagez d’installer la carte AD FS :
  - MultiFactorAuthenticationAdfsAdapterSetup64.msi
  - Registre-MultiFactorAuthenticationAdfsAdapter.ps1
  - Annuler l’inscription-MultiFactorAuthenticationAdfsAdapter.ps1
  - MultiFactorAuthenticationAdfsAdapter.config
3. Exécutez le fichier d’installation MultiFactorAuthenticationAdfsAdapterSetup64.msi.
4. Dans le programme d’installation de la carte d’authentification à plusieurs facteurs AD FS, cliquez sur **suivant** pour démarrer l’installation.
5. Lorsque l’installation est terminée, cliquez sur **Fermer** .

## <a name="edit-the-multifactorauthenticationadfsadapterconfig-file"></a>Modifiez le fichier MultiFactorAuthenticationAdfsAdapter.config

Procédez comme suit pour modifier le fichier MultiFactorAuthenticationAdfsAdapter.config :

1. Le nœud **UseWebServiceSdk** la valeur **true**.  
2. Définissez la valeur de **WebServiceSdkUrl** à l’URL du Kit de développement logiciel plusieurs facteurs d’authentification Web Service. Par exemple : *https://contoso.com/&lt;certificatename&gt;/MultiFactorAuthWebServicesSdk/PfWsSdk.asmx*, où certificatename est le nom de votre certificat.  
3. Modifiez le script de Registre-MultiFactorAuthenticationAdfsAdapter.ps1 en ajoutant *- ConfigurationFilePath &lt;chemin d’accès&gt; * à la fin de la `Register-AdfsAuthenticationProvider` commande, où * &lt;chemin d’accès&gt; * est le chemin d’accès complet au fichier MultiFactorAuthenticationAdfsAdapter.config.

### <a name="configure-the-web-service-sdk-with-a-username-and-password"></a>Configurer le Kit de développement de Service Web avec un nom d’utilisateur et un mot de passe

Il existe deux options de configuration du Kit de développement de Service Web. Le premier est un nom d’utilisateur et le mot de passe, la deuxième avec un certificat client. Procédez comme suit pour la première option, ou en aval de la seconde.  

1. La valeur de **WebServiceSdkUsername** à un compte qui est membre du groupe de sécurité PhoneFactor Admins. Utilisez le &lt;domaine&gt;& #, 92 ; &lt;nom d’utilisateur&gt; format.  
2. La valeur de **WebServiceSdkPassword** sur le mot de passe du compte approprié.

### <a name="configure-the-web-service-sdk-with-a-client-certificate"></a>Configurer le Kit de développement de Service Web avec un certificat client

Si vous ne souhaitez pas utiliser un nom d’utilisateur et le mot de passe, procédez comme suit pour configurer le Kit de développement de Service Web avec un certificat client.

1. Obtenir un certificat client auprès d’une autorité de certificat pour le serveur qui exécute le Kit de développement de Service Web. Découvrez comment [obtenir des certificats clients](https://technet.microsoft.com/library/cc770328.aspx).  
2. Importer le certificat client dans le magasin de certificats personnel d’ordinateur local sur le serveur qui exécute le Kit de développement de Service Web. Assurez-vous que le certificat de l’autorité de certification publique est dans le magasin de certificats racines approuvés.  
3. Exporter les clés publique et privées du certificat client vers un fichier .pfx.  
4. Exporter la clé publique dans le format Base64 vers un fichier .cer.  
5. Dans le Gestionnaire de serveur, vérifiez que la fonctionnalité d’authentification du mappage de certificat Client de \Web Server\Security\IIS Web Server (IIS) est installée. S’il n’est pas installé, sélectionnez **Ajout de rôles et de fonctionnalités** pour ajouter cette fonctionnalité.  
6. Dans le Gestionnaire des services Internet, double-cliquez sur **Éditeur de Configuration** du site Web qui contient le répertoire virtuel du SDK du Service Web. Il est important de le faire au niveau du site Web et non au niveau du répertoire virtuel.  
7. Passez à la section **system.webServer/security/authentication/iisClientCertificateMappingAuthentication** .  
8. Déclarez l’attribut enabled à **true**.  
9. Définissez oneToOneCertificateMappingsEnabled à **true**.  
10. Cliquez sur le bouton **...** oneToOneMappings, puis cliquez sur le lien **Ajouter** .  
11. Ouvrez le fichier .cer Base64 que précédemment exporté. Supprimer *---commencer le certificat---* *---fin certificat---*et tous les sauts de ligne. Copiez la chaîne résultante.  
12. Certificat d’ensemble de la chaîne copiée à l’étape précédente.  
13. Déclarez l’attribut enabled à **true**.  
14. Définir le nom d’utilisateur à un compte qui est membre du groupe de sécurité PhoneFactor Admins. Utilisez le &lt;domaine&gt;& #, 92 ; &lt;nom d’utilisateur&gt; format.  
15. Définir le mot de passe pour le mot de passe du compte approprié, puis fermez l’éditeur de Configuration.  
16. Cliquez sur **Appliquer** .  
17. Dans le répertoire virtuel du SDK du Service Web, double-cliquez sur **l’authentification**.  
18. Vérifiez que l’emprunt d’identité ASP.NET et l’authentification de base sont défini sur **activé**, et que tous les autres éléments sont définis sur **désactivé**.  
19. Dans le répertoire virtuel du SDK du Service Web, double-cliquez sur **Paramètres SSL**.  
20. La valeur **Accepter**les certificats clients, puis cliquez sur **Appliquer**.  
21. Copiez le fichier .pfx exporté précédemment sur le serveur qui exécute l’adaptateur AD FS.  
22. Importer le fichier .pfx dans le magasin de certificats personnel.  
23. Avec le bouton droit et sélectionnez **Gérer les clés privées**puis accorder un accès en lecture au compte que vous avez utilisé pour vous connecter au service AD FS.  
24. Ouvrir le certificat client et copiez l’empreinte à partir de l’onglet **Détails** .  
25. Dans le fichier MultiFactorAuthenticationAdfsAdapter.config, la valeur **WebServiceSdkCertificateThumbprint** à la chaîne copiée à l’étape précédente.  


Enfin, pour enregistrer l’adaptateur, exécutez le \Program Files\Multi-script de facteur d’authentification Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1 dans PowerShell. La carte est enregistrée en tant que WindowsAzureMultiFactorAuthentication. Redémarrez le service AD FS pour l’inscription prennent effet.

## <a name="related-topics"></a>Rubriques connexes

Pour la résolution des problèmes, consultez la [FAQ de l’authentification multifactorielle Azure](multi-factor-authentication-faq.md)
