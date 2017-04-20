<properties 
    pageTitle="Serveur d’authentification à plusieurs facteurs d’authentification IIS et Azure"
    description="Il s’agit de la page d’authentification Azure plusieurs facteurs qui les aidera dans le déploiement de l’authentification IIS et le serveur de l’authentification multifactorielle Azure."
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

# <a name="iis-authentication"></a>Authentification IIS

La section de l’authentification IIS du serveur d’authentification selon plusieurs facteurs Azure vous permet activer et configurer l’authentification IIS pour l’intégration avec les applications web Microsoft IIS. Le serveur de l’authentification multifactorielle Azure installe un plug-in qui peut filtrer les demandes effectuées sur le serveur web IIS pour ajouter l’authentification à plusieurs facteurs Azure. Le plug-in de IIS prend en charge l’authentification par formulaire et l’authentification intégrée Windows HTTP. Approuvé Qu'ips peuvent également être configurés pour des adresses IP internes identifiant d’authentification à deux facteurs.


![Authentification IIS](./media/multi-factor-authentication-get-started-server-iis/iis.png)


## <a name="using-form-based-iis-authentication-with-azure-multi-factor-authentication-server"></a>À l’aide de l’authentification IIS de basé sur le formulaire avec le serveur d’authentification à plusieurs facteurs Azure

Pour sécuriser une application web IIS qui utilise l’authentification par formulaire, installez le serveur de l’authentification multifactorielle Azure sur le serveur web IIS et configurer le serveur par la procédure suivante.

1. Au sein du serveur de l’authentification multifactorielle Azure, cliquez sur l’icône de l’authentification IIS dans le menu de gauche.
2. Cliquez sur l’onglet base de formulaires.
3. Cliquez sur Ajouter... bouton.
4. Pour détecter le nom d’utilisateur, les variables de mot de passe et le domaine automatiquement, entrez l’URL de connexion (par exemple, https://localhost/contoso/auth/login.aspx) dans la boîte de dialogue site Web de Auto-Configure Form-Based et cliquez sur OK.
5. La case Exiger une authentification multifactorielle utilisateur correspondance si tous les utilisateurs ont été ou seront importées dans le serveur et de l’authentification multifactorielle. Si un nombre important d’utilisateurs n’ont pas encore été importé sur le serveur et/ou est exempté de l’authentification multicritères, laissez la case désactivée.
6. Si les variables de page ne peut pas être détectées automatiquement, cliquez sur le spécifier manuellement... bouton dans la boîte de dialogue site Web de Auto-Configure Form-Based.
7. Dans la boîte de dialogue site Web de Add Form-Based, entrez l’URL de la page de connexion dans le champ URL de la présenter et entrer un nom d’Application (facultatif). Le nom de l’Application apparaît dans les rapports d’authentification à plusieurs facteurs Azure et peut être affiché dans les messages d’authentification SMS ou application Mobile. Consultez le fichier d’aide pour plus d’informations sur l’URL de la présenter.
8. Sélectionnez le format correct de la demande. Il a la valeur « POST ou GET » pour la plupart des applications web.
9. Permet d’entrer la variable de nom d’utilisateur, la variable de mot de passe et la variable de domaine (si elle s’affiche sur la page de connexion). Vous devrez accéder à la page de connexion dans un navigateur web, avec le bouton droit sur la page et sélectionnez « Afficher la Source » pour rechercher les noms des zones d’entrée de la page.
10. La case Exiger l’authentification selon plusieurs facteurs Azure utilisateur correspondance si tous les utilisateurs ont été ou seront importées dans le serveur et de l’authentification multifactorielle. Si un nombre important d’utilisateurs n’ont pas encore été importé sur le serveur et/ou est exempté de l’authentification multicritères, laissez la case désactivée. Consultez le fichier d’aide pour plus d’informations sur cette fonctionnalité.
11.  Cliquez sur avancées... bouton pour passer en revue les paramètres avancés, y compris la possibilité de sélectionner un fichier de page de refus personnalisé, pour mettre en cache les authentifications réussies sur le site Web pendant une période de temps à l’aide de cookies et d’authentifier les informations d’identification principales par rapport à un annuaire LDAP, un domaine Windows ou un serveur RADIUS. Lorsque vous avez terminé, cliquez sur le bouton OK pour revenir à la boîte de dialogue site Web de Add Form-Based. Consultez le fichier d’aide pour plus d’informations sur les paramètres avancés.
12. Cliquez sur le bouton OK.
13. Une fois les variables d’URL et de la page ont été détectés ou saisies, les données du site Web seront affiche dans le panneau à base de formulaires.
14. Consultez les IIS d’activer les Plug-ins pour Azure plusieurs facteurs d’authentification serveur ci-dessous directement pour terminer la configuration de l’authentification IIS.

## <a name="using-integrated-windows-authentication-with-azure-multi-factor-authentication-server"></a>À l’aide de l’authentification Windows intégrée avec le serveur d’authentification à plusieurs facteurs Azure

Pour sécuriser une application web IIS qui utilise l’authentification intégrée Windows HTTP, installez le serveur de l’authentification multifactorielle Azure sur le serveur web IIS et configurer le serveur par la procédure suivante.

1. Au sein du serveur de l’authentification multifactorielle Azure, cliquez sur l’icône de l’authentification IIS dans le menu de gauche.
2. Cliquez sur l’onglet HTTP. Cliquez sur l’onglet base de formulaires.
3. Cliquez sur Ajouter... bouton.
4. Dans la boîte de dialogue Ajouter une URL de Base, entrez l’URL pour le site Web où l’authentification HTTP est effectuée (par exemple, http://localhost/owa) dans le champ d’URL de Base et entrez un nom de l’Application (facultatif). Le nom de l’Application apparaît dans les rapports d’authentification à plusieurs facteurs Azure et peut être affiché dans les messages d’authentification SMS ou application Mobile.
5. Régler la temporisation d’inactivité et Maximum des durées de session si la valeur par défaut n’est pas suffisante.
6. La case Exiger une authentification multifactorielle utilisateur correspondance si tous les utilisateurs ont été ou seront importées dans le serveur et de l’authentification multifactorielle. Si un nombre important d’utilisateurs n’ont pas encore été importé sur le serveur et/ou est exempté de l’authentification multicritères, laissez la case désactivée. Consultez le fichier d’aide pour plus d’informations sur cette fonctionnalité.
7. Cochez la case de cache de cookie si vous le souhaitez.
8. Cliquez sur le bouton OK.
9. Reportez-vous à la section [IIS d’activer les Plug-ins pour Azure plusieurs facteurs d’authentification serveur](#enable-iis-plug-ins-for-azure-multi-factor-authentication-server) ci-dessous pour terminer la configuration de l’authentification IIS.


## <a name="enable-iis-plug-ins-for-azure-multi-factor-authentication-server"></a>Activer les Plug-ins de IIS pour le serveur d’authentification à plusieurs facteurs Azure

Une fois que vous avez configuré basé sur le formulaire ou l’URL HTTP de l’authentification et les paramètres, vous devez sélectionner les emplacements où les plug-ins d’Azure plusieurs facteurs d’authentification IIS doit être chargés et activés dans IIS. Utilisez la procédure suivante :

1. Si vous exécutez sur IIS 6, cliquez sur l’onglet ISAPI et sélectionnez le site Web de l’application web est en cours d’exécution sous (par exemple, Site Web par défaut) pour activer le filtre ISAPI de l’authentification multifactorielle Azure plug-in pour ce site.
2. Si vous exécutez IIS 7 ou version ultérieure, cliquez sur l’onglet Module natif et sélectionnez le serveur, les sites Web ou les applications pour activer le plug-in IIS au niveau de votre choix.
3. Cliquez sur la zone de l’authentification IIS d’activer en haut de l’écran. Authentification à plusieurs facteurs Azure la sécurisation est maintenant l’application IIS sélectionnée. Assurez-vous que les utilisateurs ont été importés dans le serveur. Reportez-vous à la section IPs de confiance ci-dessous si vous souhaitez à la liste d’autorisation que les adresses IP internes afin que l’authentification à deux facteurs n’est pas nécessaire lors de la connexion au site Web à partir de ces emplacements.


## <a name="trusted-ips"></a>Adresses IP autorisées

Les adresses IP de confiance permet aux utilisateurs de court-circuiter Azure, plusieurs facteurs d’authentification pour les demandes de site Web provenant de certaines adresses IP spécifiques ou de sous-réseaux. Par exemple, vous souhaiterez utilisateurs identifiant d’Azure, plusieurs facteurs d’authentification pendant la connexion du bureau. Pour ce faire, vous spécifiez le sous-réseau office comme une entrée IPs de confiance. Pour configurer les IPs de confiance utilisez la procédure suivante :

1. Dans la section authentification IIS, cliquez sur l’onglet IPs de confiance.
2. Cliquez sur Ajouter... bouton.
3. Lorsque la boîte de dialogue Ajouter des adresses IP de confiance s’affiche, sélectionnez l’adresse IP unique, la plage d’adresses IP ou le bouton d’option de sous-réseau.
4. Entrez l’adresse IP, plage d’adresses IP ou sous-réseau à la liste d’autorisation. Si vous entrez un sous-réseau, sélectionnez le masque de réseau approprié et cliquez sur le bouton OK. La liste d’autorisation a maintenant été ajouté.
