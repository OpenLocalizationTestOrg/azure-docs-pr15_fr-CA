<properties 
    pageTitle="États de Microsoft Azure plusieurs facteurs d’authentification utilisateur"
    description="Obtenir des informations sur les États utilisateur dans Azure AMF."
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
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="user-states-in-azure-multi-factor-authentication"></a>États utilisateur dans Azure plusieurs facteurs d’authentification

Comptes d’utilisateurs dans Azure, plusieurs facteurs d’authentification ont trois états distincts suivants :

État | Description |Applications de navigateur non affectées| Notes
:-------------: | :-------------: |:-------------: |:-------------: |
Désactivé | L’état par défaut pour un nouvel utilisateur non inscrit dans une authentification à plusieurs facteurs.|N°|L’utilisateur n’utilise pas l’authentification à plusieurs facteurs.
Activé |L’utilisateur a été inscrit dans une authentification à plusieurs facteurs.|N°  Ils continuent à fonctionner tant que le processus d’inscription est terminé.|L’utilisateur est activé, mais n’a pas terminé le processus d’inscription. Ils devront effectuer le processus à la connexion suivante.
Mise en œuvre|L’utilisateur a été inscrit et a terminé le processus d’inscription pour utiliser une authentification multifacteur.|Oui.  Les applications requièrent des mots de passe app. | L’utilisateur peut ou peut ne pas avoir terminer l’inscription. Si elles ont terminé le processus d’inscription, ils utilisent une authentification multifactorielle. Dans le cas contraire, l’utilisateur sera invité à terminer le processus à la connexion suivante.

## <a name="changing-a-user-state"></a>Modifier l’état de l’utilisateur
Modifications d’un état aux utilisateurs en fonction de si elle a été le programme d’installation de l’AMF et si l’utilisateur a terminé le processus.  Lorsque vous allumez l’AMF pour un utilisateur, les utilisateurs état passe de désactivé à activé.  Une fois que l’utilisateur, dont l’état a changé, activé, se connecte et termine le processus, son état devient appliquées.  

### <a name="to-view-a-users-state"></a>Pour afficher l’état d’un utilisateur
--------------------------------------------------------------------------------
1.  Connectez-vous à l' **Azure portal classique** en tant qu’administrateur.
2.  Sur la gauche, cliquez sur **Active Directory**.
3.  Sous, **répertoire** , cliquez sur le répertoire de l’utilisateur que vous souhaitez activer.
![Cliquez sur le répertoire](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  En haut, cliquez sur **utilisateurs**.
5.  En bas de la page, cliquez sur **Gérer les authentification selon plusieurs facteurs**.
![Cliquez sur le répertoire](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  Un nouvel onglet de navigateur s’ouvre.  Vous ne pourrez pas afficher l’état des utilisateurs.
![Cliquez sur le répertoire](./media/multi-factor-authentication-get-started-user-states/userstate1.png)

###<a name="to-change-the-state-from-disabled-to-enabled"></a>Pour modifier l’état de désactivé à activé.
1.  Connectez-vous à l' **Azure portal classique** en tant qu’administrateur.
2.  Sur la gauche, cliquez sur **Active Directory**.
3.  Sous, **répertoire** , cliquez sur le répertoire de l’utilisateur que vous souhaitez activer.
![Cliquez sur le répertoire](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  En haut, cliquez sur **utilisateurs**.
5.  En bas de la page, cliquez sur **Gérer les authentification selon plusieurs facteurs**.
![Cliquez sur le répertoire](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  Un nouvel onglet de navigateur s’ouvre.  Trouver l’utilisateur que vous souhaitez activer pour une authentification à plusieurs facteurs. Vous devrez peut-être modifier l’affichage située en haut. Vérifiez que l’état est **désactivé.** 
 ![Activer utilisateur](./media/multi-factor-authentication-get-started-cloud/enable1.png)
7.  Placez une **vérification** dans la case en regard de leur nom.
7.  Sur la droite, cliquez sur **Activer**.
![Activer utilisateur](./media/multi-factor-authentication-get-started-cloud/user1.png)
8.  Cliquez sur **Activer l’authentification de plusieurs facteur**.
![Activer utilisateur](./media/multi-factor-authentication-get-started-cloud/enable2.png)
9.  Vous noterez que l’état a changé de **désactivé** à **activé**.
![Permettre aux utilisateurs](./media/multi-factor-authentication-get-started-cloud/user.png)
10.  Une fois que vous avez activé vos utilisateurs, il est recommandé de les informer par courrier électronique.  Il doit également informer comment ils peuvent utiliser leurs applications autres que des navigateurs pour éviter d’être verrouillé.

### <a name="to-change-the-state-from-enabledenforced-to-disabled"></a>Pour modifier l’état d’activé/mise en œuvre sur désactivé
1.  Connectez-vous à l' **Azure portal classique** en tant qu’administrateur.
2.  Sur la gauche, cliquez sur **Active Directory**.
3.  Sous, **répertoire** , cliquez sur le répertoire de l’utilisateur que vous souhaitez activer.
![Cliquez sur le répertoire](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  En haut, cliquez sur **utilisateurs**.
5.  En bas de la page, cliquez sur **Gérer les authentification selon plusieurs facteurs**.
![Cliquez sur le répertoire](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  Un nouvel onglet de navigateur s’ouvre.  Trouver l’utilisateur que vous souhaitez désactiver. Vous devrez peut-être modifier l’affichage située en haut. Vérifiez que l’état est soit **activé** ou **appliquée.**
7.  Placez une **vérification** dans la case en regard de leur nom.
7.  Sur la droite, cliquez sur **désactiver**.
![Désactiver un utilisateur](./media/multi-factor-authentication-get-started-user-states/userstate2.png)
8.  Vous devrez confirmer.  Cliquez sur **Oui**.
![Désactiver un utilisateur](./media/multi-factor-authentication-get-started-user-states/userstate3.png)
9.  Vous devez ensuite voir si elle a réussi.  Cliquez sur **Fermer.** 
 ![Désactiver l’utilisateur](./media/multi-factor-authentication-get-started-user-states/userstate4.png)
