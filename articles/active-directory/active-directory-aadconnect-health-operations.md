<properties
    pageTitle="AD Azure se connecter à des opérations de santé."
    description="Cet article décrit les opérations supplémentaires qui peuvent être effectuées une fois que vous avez déployé la santé se connecter Azure AD."
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>

# <a name="azure-ad-connect-health-operations"></a>AD Azure se connecter à des opérations de santé

La rubrique suivante décrit les diverses opérations qui peuvent être effectuées à l’aide de la santé se connecter Azure AD.

## <a name="enable-email-notifications"></a>Activer les Notifications par courrier électronique
Vous pouvez configurer le Azure se connecter Health Service AD pour envoyer des notifications par courrier électronique lorsque des alertes sont générées indiquant que votre infrastructure d’identité n’est pas sain. Cela se produit lorsqu’une alerte est générée, ainsi que lorsqu’il est marqué comme résolu. Suivez les instructions ci-dessous pour configurer les notifications par courrier électronique.

![AD Azure se connecter santé E-mail Notification découvrir](./media/active-directory-aadconnect-health/email_noti_discover.png)

>[AZURE.NOTE] Notifications par courrier électronique sont désactivées par défaut.


### <a name="to-enable-azure-ad-connect-health-email-notifications"></a>Pour activer Azure AD connecter la santé par E-mail

1. Ouvrez la Blade d’alertes pour le service pour lequel vous souhaitez recevoir de notification par courrier électronique.
2. Cliquez sur le bouton « Paramètres de Notification » dans la barre d’action.
3. Activer la Notification par courrier électronique à ON.
4. Activez la case à cocher pour configurer le Global Admins pour recevoir des notifications par courrier électronique.
5. Si vous souhaitez recevoir des notifications par courrier électronique sur toutes les autres adresses de messagerie, vous pouvez les spécifier dans la zone destinataire de courrier électronique supplémentaires. Pour supprimer une adresse de messagerie de la liste, cliquez avec le bouton droit sur l’entrée et sélectionnez Supprimer.
6. Pour finaliser le modifications, cliquez sur « Enregistrer ». Les modifications prendront des effets qu’une fois que vous cliquez sur « Enregistrer ».

## <a name="delete-a-server-or-service-instance"></a>Supprimer une instance de serveur ou le service

### <a name="delete-a-server-from-azure-ad-connect-health-service"></a>Supprimer un serveur de Service des sanitaires Connect de AD Azure
Dans certains cas, vous souhaiterez peut-être supprimer un serveur surveillé. Suivez les instructions ci-dessous pour supprimer un serveur de Service des sanitaires Connect de AD Azure.

Lors de la suppression d’un serveur, prenez en compte les éléments suivants :

- Cette action arrête de collecter des données supplémentaires à partir de ce serveur. Ce serveur doit être supprimé à partir du service de surveillance. Une fois cette action, vous ne pourrez pas afficher les nouvelles alertes, de surveillance ou d’utilisation des données d’analytique pour ce serveur.
- Cette action ne sera pas désinstaller ou supprimer l’Agent de l’état de santé de votre serveur. Si vous n’avez pas désinstallé l’Agent d’intégrité avant d’effectuer cette étape, vous pouvez voir les événements d’erreur sur le serveur lié à l’Agent d’intégrité.
- Cette action ne supprimera pas les données déjà recueillies à partir de ce serveur. Ces données seront supprimées en fonction de la stratégie de rétention de données Microsoft Azure.
- Après l’exécution de cette action, si vous souhaitez démarrer la surveillance du même serveur, vous devez désinstaller et réinstaller l’agent d’intégrité sur ce serveur.


#### <a name="to-delete-a-server-from-azure-ad-connect-health-service"></a>Pour supprimer un serveur de Service des sanitaires Connect de AD Azure

Annonce Azure se connecter santé pour AD FS & AD Azure connectent (synchronisation) :

1. Ouvrez le serveur lame à partir de la liste du serveur lame en sélectionnant le nom du serveur à supprimer.
2. Sur le serveur lame, cliquez sur le bouton « Supprimer » dans la barre d’action.
3. Confirmer l’action pour supprimer le serveur en tapant le nom du serveur dans la zone de confirmation.
4. Cliquez sur le bouton « Supprimer ».

Annonce Azure se connecter santé pour AD DS :

1. Ouvrez le tableau de bord de contrôleurs de domaine.
2. Sélectionnez le contrôleur de domaine à supprimer.
3. Cliquez sur le bouton « Supprimer sélectionné » à partir de la barre d’action.
4. Confirmer l’action pour supprimer le serveur.
5. Cliquez sur le bouton « Supprimer ».

### <a name="delete-a-service-instance-from-azure-ad-connect-health-service"></a>Supprimer une instance de service à partir du Service des sanitaires Connect de AD Azure

Dans certains cas, vous souhaiterez peut-être supprimer une instance de service. Suivez les instructions ci-dessous pour supprimer une instance de service à partir du Service des sanitaires Connect de AD Azure.

Lors de la suppression d’une instance de service, prenez en compte les éléments suivants :

- Cette action va supprimer l’instance de service en cours à partir du service de surveillance.
- Cette action ne sera pas désinstaller ou supprimer les serveurs qui ont été analysés dans le cadre de cette instance de service de l’Agent d’intégrité. Si vous n’avez pas désinstallé l’Agent d’intégrité avant d’effectuer cette étape, vous pouvez voir les événements d’erreur sur les serveurs liés à l’Agent d’intégrité.
- Toutes les données de cette instance de service seront supprimées en fonction de la stratégie de rétention de données Microsoft Azure.
- Après l’exécution de cette action, si vous souhaitez démarrer le service de surveillance, veuillez désinstaller et réinstaller l’agent d’intégrité sur tous les serveurs qui seront analysés. Après l’exécution de cette action, si vous souhaitez démarrer la surveillance du même serveur, vous devez désinstaller et réinstaller l’agent d’intégrité sur ce serveur.


#### <a name="to-delete-a-service-instance-from-azure-ad-connect-health-service"></a>Pour supprimer une instance de service à partir du Service des sanitaires Connect de AD Azure

1. Ouvrez la lame de Service à partir de la Blade de liste Service en sélectionnant l’identificateur de service (nom de la batterie) que vous souhaitez supprimer.
2. Sur le serveur lame, cliquez sur le bouton « Supprimer » dans la barre d’action.
3. Vérifiez le nom du service en le tapant dans la zone de confirmation. (par exemple : sts.contoso.com)
4. Cliquez sur le bouton « Supprimer ».
<br><br>


[//]: # (Start of RBAC section)
## <a name="manage-access-with-role-based-access-control"></a>Gérer l’accès avec un rôle de base de contrôle d’accès
### <a name="overview"></a>Vue d’ensemble
[Rôle de base de contrôle d’accès](role-based-access-control-configure.md) pour la santé se connecter Azure AD fournit un accès service de santé Azure AD se connecter pour les utilisateurs et/ou les groupes en dehors des administrateurs globaux. Cela est obtenue en attribuant des rôles aux groupes et/ou utilisateurs prévus et fournit un mécanisme pour limiter les administrateurs globales au sein de votre répertoire.

#### <a name="roles"></a>Rôles
Santé de connexion AD Azure prend en charge les rôles prédéfinis suivants.

| Rôle | Autorisations |
| ----------- | ---------- |
| Propriétaire | Les propriétaires peuvent ***gérer l’accès*** (par exemple, le rôle d’attribuer à un utilisateur/groupe), ***permet d’afficher toutes les informations*** (par exemple, afficher les alertes) à partir du portail et ***Modifier les paramètres*** (par exemple, les notifications par courrier électronique) au sein de la santé se connecter Azure AD. <br>Par défaut, les administrateurs globaux AD Azure sont affectés à ce rôle et il ne peut pas être modifié.  |
|Collaborateur|  Les collaborateurs peuvent ***Afficher toutes les informations*** (par exemple, afficher les alertes) à partir du portail et ***Modifier les paramètres*** (par exemple, les notifications par courrier électronique) au sein de la santé se connecter Azure AD.|
|Lecteur| Les lecteurs peuvent ***Afficher toutes les informations*** (par exemple, afficher les alertes) à partir du portail, au sein de la santé se connecter Azure AD.|

Tous les autres rôles (par exemple, les administrateurs de l’accès utilisateur ou « Utilisateurs de laboratoires DevTest »), même si elle est disponible dans l’utilisation du portail, avoir aucun impact dans la santé se connecter Azure AD.

#### <a name="access-scope"></a>Portée de l’accès

Azure AD Connect prend en charge deux niveaux de gestion :

- ***Toutes les instances de service***: il s’agit du chemin d’accès recommandé pour la plupart des clients et contrôle l’accès pour toutes les instances de service (par exemple, une batterie de serveurs ADFS) pour tous les types de rôles qui sont surveillées par AD Azure se connecter la santé.

- ***L’Instance de service***: dans certains cas, vous devrez peut-être séparer l’accès basé sur les types de rôles, ou par une instance de service. Dans ce cas, vous pouvez gérer l’accès au niveau de l’instance de service.  

Autorisation est accordée si un utilisateur final a accès soit au niveau du répertoire ou de l’Instance de Service.


### <a name="how-to-allow-users-or-groups-access-to-azure-ad-connect-health"></a>Comment faire pour autoriser l’accès les utilisateurs ou les groupes pour la santé se connecter Azure AD
#### <a name="steps-1-select-the-appropriate-access-scope"></a>Étapes 1 : Sélectionner l’étendue de l’accès approprié
Pour permettre à un utilisateur l’accès au niveau de *toutes les instances de service* dans la santé se connecter Azure AD, ouvrez la lame principale dans la santé se connecter Azure AD.<br>
#### <a name="step-2-add-users-groups-and-assign-roles"></a>Étape 2 : Ajouter des utilisateurs, des groupes et attribuer des rôles
1. Cliquez sur la partie « Utilisateurs » de la section de configuration.<br>
![Annonce Azure se connecter lame principale de santé RBAC](./media/active-directory-aadconnect-health/RBAC_main_blade.png)
2. Sélectionnez « Ajouter »
3. Sélectionnez le « rôle » comme « Propriétaire »<br>
![Annonce Azure se connecter santé RBAC Ajouter utilisateur](./media/active-directory-aadconnect-health/RBAC_add.png)
4. Tapez le nom ou l’identificateur de groupe ou l’utilisateur cible. Vous pouvez sélectionner un ou plusieurs utilisateurs ou groupes en même temps. Cliquez sur « Sélectionner ».
![AD Azure santé RBAC sélectionnez un utilisateur de se connecter](./media/active-directory-aadconnect-health/RBAC_select_users.png)
5. Sélectionnez « Ok ».<br>

6. Une fois que l’affectation de rôle est terminée, les utilisateurs et/ou les groupes seront affiche dans la liste.<br>
![Annonce Azure se connecter santé RBAC liste de l’utilisateur](./media/active-directory-aadconnect-health/RBAC_user_list.png)

Ces étapes permettra l’accès de groupe en fonction de leurs rôles et les utilisateurs répertoriés.
>[AZURE.NOTE]
- Les administrateurs généraux disposent toujours d’un accès complet à toutes les opérations, mais les comptes d’administrateur global ne sera pas présents dans la liste ci-dessus.
- « Inviter des utilisateurs » de fonctionnalité n’est pas pris en charge dans Azure santé de connexion AD.

#### <a name="step-3-share-the-blade-location-with-users-or-groups"></a>Étape 3 : Partage de l’emplacement de la lame avec des utilisateurs ou des groupes
1. Après l’affectation des autorisations, un utilisateur peut accéder Azure santé de connexion AD en allant dans [http://aka.ms/aadconnecthealth](http://aka.ms/aadconnecthealth).
2. Une fois sur la lame, l’utilisateur peut épingler la lame ou différentes parties au tableau de bord en cliquant simplement sur « Épingler au tableau de bord »<br>
![Lame de broche Azure RBAC santé de connexion AD](./media/active-directory-aadconnect-health/RBAC_pin_blade.png)


>[AZURE.NOTE] Un utilisateur avec le rôle de « Lecteur » attribué ne sera pas en mesure d’effectuer l’opération « créer » pour obtenir les extension de santé de connexion AD Azure dans la Azure Marketplace. Cet utilisateur peut toujours accéder à la lame en cliquant sur le lien ci-dessus. Pour une utilisation ultérieure, l’utilisateur peut épingler la lame au tableau de bord.

### <a name="remove-users-andor-groups"></a>Supprimer les utilisateurs et/ou groupes
Vous pouvez supprimer un utilisateur ou un groupe à la partie d’Azure AD connecter santé rôle contrôle d’accès par clic droit et en sélectionnant Supprimer.<br>
![Annonce Azure se connecter santé RBAC suppression utilisateur](./media/active-directory-aadconnect-health/RBAC_remove.png)

[//]: # (End of RBAC section)

## <a name="related-links"></a>Liens connexes

* [Annonce Azure se connecter à la santé](active-directory-aadconnect-health.md)
* [AD Azure se connecter l’Installation de l’Agent de la santé](active-directory-aadconnect-health-agent-install.md)
* [À l’aide d’Active Directory Azure se connecter la santé avec AD FS](active-directory-aadconnect-health-adfs.md)
* [À l’aide de la santé se connecter Azure AD pour la synchronisation](active-directory-aadconnect-health-sync.md)
* [À l’aide d’Active Directory Azure se connecter la santé avec les services AD DS](active-directory-aadconnect-health-adds.md)
* [Annonce Azure se connecter le Forum aux questions sur la santé](active-directory-aadconnect-health-faq.md)
* [Annonce Azure se connecter à l’historique de Version de la santé](active-directory-aadconnect-health-version-history.md)
