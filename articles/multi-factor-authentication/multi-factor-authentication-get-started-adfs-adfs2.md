<properties
    pageTitle="Utiliser le serveur de l’AMF Azure avec AD FS 2.0 | Microsoft Azure"
    description="Il s’agit de la page d’authentification Azure plusieurs facteurs qui décrit la procédure de mise en route avec Azure AMF et AD FS 2.0."
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

# <a name="secure-cloud-and-on-premises-resources-using-azure-multi-factor-authentication-server-with-ad-fs-20"></a>Sécuriser les ressources de cloud et sur site à l’aide du serveur de l’authentification multifactorielle Azure avec AD FS 2.0

Cet article est pour les organisations fédérées avec Azure Active Directory et want sécuriser des ressources qui se trouvent sur site ou dans le nuage. Protégez vos ressources en utilisant le serveur de l’authentification multifactorielle Azure et en configurant ce qu’il fonctionne avec AD FS afin que la vérification d’en deux étapes est déclenchée pour les points de terminaison de valeur élevée.

Cette documentation traite à l’aide du serveur de l’authentification multicritères Azure avec AD FS 2.0.  Obtenir plus d’informations sur les [ressources de sécurisation nuage et sur site à l’aide du serveur de l’authentification multifactorielle Azure avec Windows Server 2012 R2 AD FS](multi-factor-authentication-get-started-adfs-w2k12.md).


## <a name="secure-ad-fs-20-with-a-proxy"></a>Sécuriser AD FS 2.0 avec un proxy
Pour sécuriser AD FS 2.0 avec un proxy, installez le serveur de l’authentification multifactorielle Azure sur le serveur de proxy ADFS et configurer le serveur.

### <a name="configure-iis-authentication"></a>Configurer l’authentification IIS

1. Au sein du serveur de l’authentification multifactorielle Azure, cliquez sur l’icône de **L’authentification IIS** dans le menu de gauche.
2. Cliquez sur l’onglet **Base de formulaires** .
3. Cliquez sur l' **Ajouter...** bouton.
<center>![Programme d’installation](./media/multi-factor-authentication-get-started-adfs-adfs2/setup1.png)</center>
4. Pour détecter automatiquement les variables de domaine, nom d’utilisateur et mot de passe, entrez l’URL de connexion (comme https://sso.contoso.com/adfs/ls) dans la boîte de dialogue site Web de Auto-Configure Form-Based et cliquez sur OK.
5. La case **requièrent l’authentification selon plusieurs facteurs Azure utilisateur correspondent** si tous les utilisateurs ont été ou seront importés sur le serveur et à la vérification de deux étapes. Si un nombre important d’utilisateurs n’ont pas encore été importé sur le serveur et/ou sera exempté de la vérification de deux étapes, laissez la case désactivée. Pour plus d’informations sur cette fonctionnalité, consultez le fichier d’aide.
6. Si les variables de page ne peut pas être détectées automatiquement, cliquez sur le **Spécifier manuellement...** bouton dans la boîte de dialogue site Web de Auto-Configure Form-Based.
7. Dans la boîte de dialogue site Web de Add Form-Based, entrez l’URL de la page de connexion ADFS dans le champ Envoyer une URL (par exemple, https://sso.contoso.com/adfs/ls) et entrez un nom de l’Application (facultatif). Le nom de l’Application apparaît dans les rapports d’authentification à plusieurs facteurs Azure et peut être affiché dans les messages d’authentification SMS ou application Mobile. Consultez le fichier d’aide pour plus d’informations sur l’URL de la présenter.
8. Définir le format de la demande en « Valider ou obtenir ».
9. Entrez le nom d’utilisateur (ctl00 ContentPlaceHolder1$ $UsernameTextBox) et la variable mot de passe (ctl00 ContentPlaceHolder1$ $PasswordTextBox). Si votre page de connexion à base de formulaire affiche une zone de texte domaine, entrez la variable domaine également. Vous devrez accéder à la page de connexion dans un navigateur web, avec le bouton droit sur la page et sélectionnez **Afficher la Source** pour rechercher les noms des zones d’entrée dans la page de connexion.
10. La case **requièrent l’authentification selon plusieurs facteurs Azure utilisateur correspondent** si tous les utilisateurs ont été ou seront importés sur le serveur et à la vérification de deux étapes. Si un nombre important d’utilisateurs n’ont pas encore été importé sur le serveur et/ou sera exempté de la vérification de deux étapes, laissez la case désactivée.
<center>![Programme d’installation](./media/multi-factor-authentication-get-started-adfs-adfs2/manual.png)</center>
11. Cliquez sur l' **avancée...** bouton pour passer en revue les paramètres avancés. Vous pouvez configurer les paramètres y compris la possibilité de sélectionner un fichier de page de refus personnalisé, pour mettre en cache les authentifications réussies sur le site Web à l’aide de cookies et de sélectionner comment authentifier les informations d’identification primaires.
12. Dans la mesure où le serveur de proxy ADFS n’est pas susceptible d’être joint au domaine, vous pouvez utiliser LDAP pour se connecter à votre contrôleur de domaine pour l’importation de l’utilisateur et de l’authentification préalable. Dans la boîte de dialogue site Web de Advanced Form-Based, cliquez sur l’onglet **Authentification principal** et sélectionnez la **Liaison LDAP** pour le type d’authentification avant l’authentification.
13. Lorsque vous avez terminé, cliquez sur le bouton **OK** pour revenir à la boîte de dialogue site Web de Add Form-Based. Consultez le fichier d’aide pour plus d’informations sur les paramètres avancés.
14. Cliquez sur le bouton **OK** pour fermer la boîte de dialogue.
15. Une fois que les variables d’URL et de la page ont été détectés ou saisies, les données de site Web s’affiche dans le panneau de basé sur le formulaire.
16. Cliquez sur l’onglet **Module natif** et sélectionnez le serveur, le site Web qui exécute le proxy ADFS sous (comme « Site Web par défaut ») ou l’application de proxy ADFS (comme des « ls » sous « adfs ») pour activer le plug-in IIS au niveau souhaité.
17. Cliquez sur la case de **l’authentification IIS d’activer** en haut de l’écran.
18. L’authentification IIS est maintenant activée.

### <a name="configure-directory-integration"></a>Configurer l’intégration d’annuaire

Vous avez activé l’authentification IIS, mais pour effectuer l’authentification préalable pour votre Active Directory (AD) via LDAP, vous devez configurer la connexion LDAP au contrôleur de domaine.

1. Cliquez sur l’icône **d’Intégration d’annuaire** .
2. Sous l’onglet Paramètres, sélectionnez le bouton radio **utiliser une configuration LDAP spécifique** .
<center>![Programme d’installation](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap1.png)</center>
3. Cliquez sur le **Modifier...** bouton.
4. Dans la boîte de dialogue Modifier la Configuration de LDAP, remplissez les champs avec les informations requises pour se connecter au contrôleur de domaine Active Directory. Descriptions des champs sont incluses dans le tableau ci-dessous. Cette information est également incluse dans le fichier d’aide Azure plusieurs facteurs d’authentification serveur.
5. Tester la connexion LDAP en cliquant sur le bouton **Test** .
<center>![Programme d’installation](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap2.png)</center>
6. Si le test de la connexion LDAP a réussi, cliquez sur le bouton **OK** .

### <a name="configure-company-settings"></a>Configurer les paramètres de la société

1. Ensuite, cliquez sur l’icône des **Paramètres de la société** et sélectionnez l’onglet **Résolution de nom d’utilisateur** .
2. Sélectionnez le bouton radio **attribut d’identificateur unique utiliser LDAP pour les noms d’utilisateurs correspondant** .
3. Si les utilisateurs entrent leur nom d’utilisateur dans le format « DOMAINE\nom_utilisateur », le serveur doit être en mesure de supprimer le domaine sur le nom d’utilisateur lorsqu’il crée la requête LDAP. Qui peut être effectuée via un paramètre du Registre.
4. Ouvrez l’Éditeur du Registre et accédez à HKEY_LOCAL_MACHINE/SOFTWARE/Wow6432Node/positif réseaux/PhoneFactor sur un serveur 64 bits. Dans le cas d’un serveur 32 bits, prendre le « Wow6432Node » sur le chemin d’accès. Créer une clé de Registre DWORD appelée « UsernameCxz_stripPrefixDomain » et définissez la valeur sur 1. Authentification à plusieurs facteurs Azure la sécurisation est maintenant le proxy ADFS.

Assurez-vous que les utilisateurs ont été importés à partir d’Active Directory sur le serveur. Si vous souhaitez que les adresses IP internes d’autorisation afin que la vérification de deux étapes n’est pas requise lorsque vous vous connectez au site Web à partir de ces emplacements, reportez-vous à la [section de l’IPs de confiance](#trusted-ips) .

<center>![Programme d’installation](./media/multi-factor-authentication-get-started-adfs-adfs2/reg.png)</center>

## <a name="ad-fs-20-direct-without-a-proxy"></a>AD FS 2.0 directement sans proxy

Vous pouvez sécuriser AD FS lorsque le proxy de Federation Services n’est pas utilisé. Installer le serveur de l’authentification multifactorielle Azure sur le serveur AD FS et configurer le serveur par les étapes suivantes :

1. Au sein du serveur de l’authentification multifactorielle Azure, cliquez sur l’icône de **L’authentification IIS** dans le menu de gauche.
2. Cliquez sur l’onglet **HTTP** .
3. Cliquez sur l' **Ajouter...** bouton.
4. Dans la boîte de dialogue Ajouter une URL de Base, entrez l’URL pour le site Web d’ADFS dans lequel l’authentification HTTP est effectuée (par exemple, https://sso.domain.com/adfs/ls/auth/integrated) dans le champ d’URL de Base. Ensuite, entrez un nom de l’Application (facultatif). Le nom de l’Application apparaît dans les rapports d’authentification à plusieurs facteurs Azure et peut être affiché dans les messages d’authentification SMS ou application Mobile.
5. Si vous le souhaitez, modifier le délai d’inactivité et Maximum des durées de session.
6. La case **requièrent l’authentification selon plusieurs facteurs Azure utilisateur correspondent** si tous les utilisateurs ont été ou seront importés sur le serveur et à la vérification de deux étapes. Si un nombre important d’utilisateurs n’ont pas encore été importé sur le serveur et/ou sera exempté de la vérification de deux étapes, laissez la case désactivée. Pour plus d’informations sur cette fonctionnalité, consultez le fichier d’aide.
7. Cochez la case de cache de cookie si vous le souhaitez.
<center>![Programme d’installation](./media/multi-factor-authentication-get-started-adfs-adfs2/noproxy.png)</center>
8. Cliquez sur le bouton **OK** .
9. Cliquez sur l’onglet **Module natif** et sélectionnez le serveur, le site Web (comme « Site Web par défaut ») ou l’application ADFS (par exemple, « ls » sous « adfs ») pour activer le plug-in IIS au niveau souhaité.
10. Cliquez sur la case de **l’authentification IIS d’activer** en haut de l’écran. Authentification à plusieurs facteurs Azure la sécurisation est maintenant ADFS.

Assurez-vous que les utilisateurs ont été importés à partir d’Active Directory sur le serveur. Si vous souhaitez que les adresses IP internes d’autorisation afin que la vérification de deux étapes n’est pas requise lorsque vous vous connectez au site Web à partir de ces emplacements, reportez-vous à la section d’IPs de confiance.


## <a name="trusted-ips"></a>Adresses IP autorisées
Adresses IP autorisées permettent aux utilisateurs de court-circuiter Azure, plusieurs facteurs d’authentification pour les demandes de site Web provenant d’adresses IP ou des sous-réseaux spécifiques. Par exemple, vous souhaiterez utilisateurs exemptes de la vérification des deux étapes lorsqu’ils se connectent à partir de l’office. Pour ce faire, vous spécifiez le sous-réseau office comme une entrée IPs de confiance.

### <a name="to-configure-trusted-ips"></a>Pour configurer des adresses IP autorisées


1. Dans la section authentification IIS, cliquez sur l’onglet **IPs de confiance** .
1. Cliquez sur l' **Ajouter...** bouton.
1. Lorsque la boîte de dialogue Ajouter des adresses IP de confiance s’affiche, sélectionnez un des boutons radio **IP unique**, **plage d’adresses IP**ou **sous-réseau** .
1. Entrez l’adresse IP, plage d’adresses IP ou sous-réseau à la liste d’autorisation. Si vous entrez un sous-réseau, sélectionnez le masque de réseau approprié et cliquez sur le bouton **OK** . La période d’enquête de confiance a maintenant été ajouté.


<center>![Programme d’installation](./media/multi-factor-authentication-get-started-adfs-adfs2/trusted.png)</center>
