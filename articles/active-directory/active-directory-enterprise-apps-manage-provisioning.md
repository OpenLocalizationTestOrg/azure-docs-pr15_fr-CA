<properties
    pageTitle="Approvisionnement de gestion pour les applications d’entreprise dans l’aperçu Azure Active Directory de l’utilisateur | Microsoft Azure"
    description="Apprenez à gérer le service de compte utilisateur pour les applications d’entreprise à l’aide de l’aperçu d’Azure Active Directory"
    services="active-directory"
    documentationCenter=""
    authors="asmalser"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/12/2016"
    ms.author="asmalser"/>

#<a name="preview-managing-user-account-provisioning-for-enterprise-apps-in-the-new-azure-portal"></a>Aperçu : Gestion de compte d’utilisateur pour les applications d’entreprise dans le nouveau portail Azure

Cet article décrit comment utiliser le [portail Azure](https://portal.azure.com) pour gérer le compte d’utilisateur automatique de mise en service et mise hors service pour les applications qui prennent en charge, en particulier celles qui ont été ajoutés dans la catégorie « featured » de la [Galerie d’applications Azure Active Directory](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery). Cette expérience de gestion dans le nouveau portail Azure est en cours d’évaluation, et que cet article décrit les nouvelles fonctionnalités, ainsi que quelques limitations temporaires qui sont en place au cours de la période d’aperçu. [Nouveautés dans l’aperçu](active-directory-preview-explainer.md)

Pour plus d’informations sur la configuration de compte automatique de l’utilisateur et de son fonctionnement, voir [automatiser la mise en service des utilisateurs et Deprovisioning aux Applications SaaS avec Azure Active Directory](active-directory-saas-app-provisioning.md).

##<a name="finding-your-apps-in-the-new-portal"></a>Recherche de vos applications dans le nouveau portail

À partir de septembre 2016, toutes les applications qui ont été configurées pour une seule ouverture de session dans un répertoire, par un administrateur de l’annuaire à l’aide de la [Galerie d’applications Azure Active Directory](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery) à l’intérieur du [Azure portal classique](https://manage.windowsazure.com), peuvent maintenant consulter et gérer dans le nouveau portail Azure.

Ces applications sont accessibles dans la section **Des Applications d’entreprise** du nouveau portail Azure, qui est accessible via le menu de **Plus de Services** dans la zone de navigation gauche. Les applications d’entreprise sont des applications qui ont été déployées et utilisées par les utilisateurs au sein de votre organisation.

![Lame d’Applications d’entreprise][0]

Cliquez sur le lien de **toutes les applications** sur la gauche affiche la liste de toutes les applications qui ont été configurés, y compris les applications qui avaient été ajoutées à partir de la galerie. La sélection d’une application charge la lame de la ressource pour cette application, où les rapports peuvent être affichées pour cette application et une variété de paramètres peut être gérée.

Configuration des paramètres de compte d’utilisateur peut être géré en sélectionnant **le provisionnement** sur la gauche.

![Lame de ressources d’application][1]


##<a name="provisioning-modes"></a>Modes de mise en service

La lame de **Provisioning** commence avec un **Mode** de menu, qui affiche les modes de mise en service sont pris en charge pour une application d’entreprise et leur permet d’être configuré. Les options disponibles sont les suivantes :

* **Automatique** , cette option apparaît si AD Azure prend en charge basée sur l’API de mise en service automatique ou mise hors service des comptes d’utilisateurs à cette application. La sélection de ce mode affiche une interface qui guide les administrateurs dans la configuration AD Azure pour se connecter à l’API de gestion des utilisateurs de l’application, la création de mappages de compte et de flux de travail qui définissent la façon dont les données de compte d’utilisateur doivent transitent AD Azure et l’application et la gestion de la mise en service du service de publicité Azure.

* **Manuel** - cette option est affichée si l’annonce Azure ne gère pas la mise en service automatique des comptes d’utilisateurs à cette application. Cette option signifie que les enregistrements de compte d’utilisateur stockés dans l’application doivent être administrés à l’aide d’un processus externe, selon les fonctionnalités de gestion et le provisionnement utilisateur fournies par cette application (qui peut inclure la mise en service de SAML Just).


##<a name="configuring-automatic-user-account-provisioning"></a>Configuration de la mise en service de compte automatique de l’utilisateur

Si vous sélectionnez l’option **automatique** affiche un écran qui est divisé en quatre sections :

###<a name="admin-credentials"></a>Informations d’identification de l’administrateur
Voici où les informations d’identification requises pour Azure AD pour se connecter à la gestion des utilisateurs de l’application API sont entrés. L’entrée requise varie en fonction de l’application. Pour en savoir plus sur les types d’informations d’identification et les exigences pour des applications spécifiques, consultez le [didacticiel de configuration pour cette application spécifique](active-directory-saas-app-provisioning.md#list-of-apps-that-support-automated-user-provisioning).

Cliquez sur le bouton **Tester la connexion** vous permet de tester les informations d’identification grâce à Azure Active Directory tente de se connecter à l’application de mise en service application utilisant les informations d’identification fournies.

###<a name="mappings"></a>Mappages
Il s’agit d’afficher et de modifier le flux d’attributs utilisateur entre AD Azure où admins et l’application cible, lors de la mise en service ou de la mise à jour des comptes d’utilisateurs.

Il existe un jeu préconfiguré de mappages entre des objets d’utilisateur AD Azure et de chaque application SaaS. Certaines applications gérer d’autres types d’objets, tels que des groupes ou des Contacts. Sélectionnez une des ces mappages dans le tableau indique l’éditeur de mappage vers la droite, où elles peuvent être affichées et personnalisés.

![Lame de ressources d’application][2]

Personnalisations prises en charge lors de la première visualisation sont les suivantes :

* Activation et désactivation des mappages pour des objets spécifiques, tels que l’objet d’utilisateur AD Azure pour l’objet utilisateur de l’application SaaS.

* Modifier les attributs de flux à partir de l’objet utilisateur Active Directory Azure pour l’objet utilisateur de l’application. Pour plus d’informations sur le mappage d’attributs, voir [Présentation des types de correspondance attribut](active-directory-saas-customizing-attribute-mappings.md#understanding-attribute-mapping-types).

* Filtrer les actions de mise en service AD Azure doit effectuer dans l’application cible, qui est une nouvelle fonctionnalité dans le portail Azure. Au lieu d’Azure AD-synchroniser entièrement les objets, vous pouvez limiter les actions exécutées. Par exemple, par la seule sélection **mise à jour**, Azure annonce uniquement mises à jour utilisateur existant dans une application de comptes et ne crée pas de nouvelles. En sélectionnant uniquement les **créer**, Azure seulement crée des comptes d’utilisateur, mais ne met pas à jour existantes. Cette fonctionnalité permet de créer des mappages de différentes pour la création de comptes et de mettre à jour les flux de travail. La possibilité de créer plusieurs mappages par application complète est prévue ultérieurement dans la période d’aperçu.

###<a name="settings"></a>Paramètres
Cette section permet d’administrateurs démarrer et arrêter la publicité Azure mise en service du service pour l’application sélectionnée, ainsi que si vous le souhaitez effacer le cache de configuration et redémarrer le service.

Si la mise en service est activé pour la première fois pour une application, activer le service en modifiant l' **État de mise en service** sur **On**. Cela provoque la mise en service du service de publicité Azure effectuer une synchronisation initiale, où il lit les utilisateurs affectés dans la section **utilisateurs et groupes** , interroge l’application cible pour les et effectue ensuite les actions de configuration définies dans la section Azure AD **mappages** . Pendant ce processus, le service d’approvisionnement stocke les données en mémoire cache sur les comptes d’utilisateurs qu’il gère, afin que les comptes de non managé à l’intérieur les applications cibles qui ont été jamais dans la portée d’affectation ne sont pas affectés par la mise hors service des opérations. Après la synchronisation initiale, le service d’approvisionnement synchronise automatiquement les objets utilisateur et groupe sur un intervalle de dix minutes.

Modification de l' **État de mise en service** à **désactiver** suspend simplement le service de mise en service. Dans cet état, Azure ne pas créer, mettre à jour ou supprimer les objets utilisateur ou groupe dans l’application. Modification de l’état à la suite provoque le service reprendre là où elle s’était arrêtée.

Sélection de la case à cocher **Effacer l’état en cours et redémarrez la synchronisation** et l’enregistrement s’arrête le service de mise en service, dumps, les données mises en cache sur les comptes Active Directory Azure gère, redémarre les services et effectue la synchronisation initiale à nouveau. Cette option permet aux administrateurs recommencer le processus de déploiement de mise en service à nouveau.

###<a name="synchronization-details"></a>Détails de la synchronisation
Cette section fournit plus plus d’informations sur l’opération du service de mise en service, y compris les heures des premier et derniers exécuté le service de mise en service de l’application et comment de nombreux objets utilisateur et groupe sont gérés.

Des liens sont fournis pour le **rapport d’activité de provisionnement**, qui fournit un journal de tous les utilisateurs et groupes créé, mis à jour et supprimé entre AD Azure et détaillée de la cible d’application et le **rapport d’erreurs de provisionnement** qui fournit plus des messages d’erreur pour les objets utilisateur et groupe qui n’a pas pu être lues, créées, mises à jour ou supprimé. 

[0]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-blade.PNG
[1]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-provisioning.PNG
[2]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-provisioning-mapping.PNG
