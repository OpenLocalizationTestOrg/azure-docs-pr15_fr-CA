<properties
    pageTitle="Quels sont les mots de passe application dans Azure AMF ?"
    description="Cette page permettra aux utilisateurs de comprendre quels sont les mots de passe app et à quoi elles servent avec respect Azure AMF."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>



# <a name="what-are-app-passwords-in-azure-multi-factor-authentication"></a>Quels sont les mots de passe application dans Azure, plusieurs facteurs d’authentification ?

Certaines applications autres que des navigateurs, tels que le client de messagerie électronique natif Apple qui utilise Exchange Active Sync, ne prennent actuellement pas en charge authentification à plusieurs facteurs. Plusieurs facteurs d’authentification est activé par l’utilisateur. Cela signifie que si un utilisateur a été activé pour l’authentification à facteurs multiples et ils tentent d’utiliser les applications autres que des navigateurs, ils ne pourront faire. Un mot de passe d’application permet à ce problème.

>[AZURE.NOTE] Authentification des Clients Office 2013 moderne
>
> Les clients Office 2013 (y compris Outlook) prise en charge de nouveaux protocoles d’authentification maintenant et peuvent être activés pour prendre en charge d’authentification à plusieurs facteurs.  Cela signifie qu’une fois activé, les mots de passe app ne sont pas nécessaires pour une utilisation avec les clients Office 2013.  Pour plus d’informations voir [Office 2013 d’authentification modernes public preview a annoncé](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

## <a name="how-to-use-app-passwords"></a>L’utilisation de mots de passe app

Voici quelques points à retenir sur l’utilisation de mots de passe app.

- Le mot de passe est généré automatiquement et n’est pas fourni par l’utilisateur. En effet, le mot de passe généré automatiquement, est plus difficile pour un intrus de deviner et est plus sécurisée.
- Actuellement il existe une limite de 40 mots de passe par utilisateur. Si vous tentez de créer une fois vous avez atteint la limite, vous devrez supprimer l’un de vos mots de passe d’application existant pour créer un nouveau.
- Il est recommandé de créer des mots de passe app par périphérique et non par l’application. Par exemple, vous pouvez créer un mot de passe app pour votre ordinateur portable et utilisez ce mot de passe d’application pour toutes les applications sur l’ordinateur.
- Vous obtenez un mot de passe d’application la première fois vous reconnectez-vous.  Si vous avez besoin de nouvelles, vous pouvez les créer.

![Programme d’installation](./media/multi-factor-authentication-end-user-app-passwords/app.png)

Une fois que vous avez un mot de passe d’application, vous l’utilisez à la place de votre mot de passe d’origine avec ces applications autres que des navigateurs.  Par exemple, si vous utilisez une authentification multifactorielle et le client de messagerie natif de Apple sur votre téléphone.  Utilisez le mot de passe d’application afin qu’elle peut ignorer l’authentification à facteurs multiples et continuer à travailler.

## <a name="creating-and-deleting-app-passwords"></a>Création et suppression de mots de passe app
Pendant votre connexion initiale, vous obtenez un mot de passe d’application que vous pouvez utiliser.  En outre, vous pouvez également créer et supprimer des mots de passe app plus tard.  Comment cela dépend de la façon dont vous utilisez une authentification multifactorielle.  Choisissez celle que la plupart s’applique à vous.

Utilisation de plusieurs facteur authentiation|Description
:------------- | :------------- |
[Utiliser avec Office 365](#creating-and-deleting-app-passwords-with-office-365)|  Cela signifie que vous souhaitez créer des mots de passe app via le portail Office 365.
[Je ne sais pas](#creating-and-deleting-app-passwords-with-myapps-portal)|Cela signifie que vous pouvez créer des mots de passe app [https://myapps.microsoft.com](https://myapps.microsoft.com)
[L’utiliser avec Microsoft Azure](#create-app-passwords-in-the-azure-portal)| Cela signifie que vous devez créer des mots de passe app via le portail Azure.

## <a name="creating-and-deleting-app-passwords-with-office-365"></a>Création et suppression de mots de passe app avec Office 365

Si vous utilisez une authentification multifactorielle avec Office 365, vous souhaiterez créer et supprimer des mots de passe app via le portail Office 365.

### <a name="to-create-app-passwords-in-the-office-365-portal"></a>Pour créer des mots de passe app dans le portail Office 365
--------------------------------------------------------------------------------

1. Ouvrez une session sur le [portail Office 365](https://login.microsoftonline.com/).
2. Dans le coin supérieur droit, sélectionnez le widget et choisissez les paramètres d’Office 365.
3. Cliquez sur Vérification de la sécurité supplémentaires.
4. Sur la droite, cliquez sur le lien indiquant **Mes numéros de téléphone utilisés pour la sécurité d’un compte de mise à jour.** 
 ![Le programme d’installation](./media/multi-factor-authentication-end-user-manage/o365a.png)
5. Ceci vous dirigera vers la page qui vous permet de modifier vos paramètres.
![Nuage](./media/multi-factor-authentication-end-user-manage/o365b.png)
6. En haut, en regard de la vérification de sécurité supplémentaires, cliquez sur **les mots de passe app.**
7. Cliquez sur **créer**.
![Nuage](./media/multi-factor-authentication-end-user-app-passwords-create-o365/apppass.png)
8. Entrez un nom pour le mot de passe d’application et cliquez sur **suivant**.
![Créer un mot de passe d’application](./media/multi-factor-authentication-end-user-app-passwords/create1.png)
9. Copier le mot de passe d’application dans le Presse-papiers et les coller dans votre application.
![Créer un mot de passe d’application](./media/multi-factor-authentication-end-user-app-passwords/create2.png)


### <a name="to-delete-app-passwords-using-the-office-365-portal"></a>Pour supprimer les mots de passe app via le portail Office 365
--------------------------------------------------------------------------------


1. Ouvrez une session sur le [portail Office 365](https://login.microsoftonline.com/).
2. Dans le coin supérieur droit, sélectionnez le widget et choisissez les paramètres d’Office 365.
3. Cliquez sur Vérification de la sécurité supplémentaires.
4. Sur la droite, cliquez sur le lien indiquant **Mes numéros de téléphone utilisés pour la sécurité d’un compte de mise à jour.** 
 ![Le programme d’installation](./media/multi-factor-authentication-end-user-manage/o365a.png)
5. Ceci vous dirigera vers la page qui vous permet de modifier vos paramètres.
![Nuage](./media/multi-factor-authentication-end-user-manage/o365b.png)
6. En haut, en regard de la vérification de sécurité supplémentaires, cliquez sur **les mots de passe app.**
7. En regard du mot de passe d’application que vous souhaitez supprimer, cliquez sur **Supprimer**.
![Supprimer un mot de passe d’application](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)
8. Cliquez sur **Oui**pour confirmer la suppression.
![Confirmer la suppression](./media/multi-factor-authentication-end-user-app-passwords/delete2.png)
9. Si vous supprimez le mot de passe d’application, vous pouvez cliquer sur **Fermer**.
![Fermer](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)


## <a name="creating-and-deleting-app-passwords-with-myapps-portal"></a>Création et suppression de mots de passe app Myapps portal.
Si vous n’êtes pas sûr de la façon dont vous utilisez l’authentification multicritères, vous pouvez toujours créer et supprimer des mots de passe app via le portail myapps.

### <a name="to-create-an-app-password-using-the-myapps-portal"></a>Pour créer un mot de passe de l’application via le portail Myapps

1. Ouvrez une session dans [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Dans la partie supérieure, sélectionnez le profil.
3. Sélectionnez la vérification de sécurité supplémentaires.
![Nuage](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. Ceci vous dirigera vers la page qui vous permet de modifier vos paramètres.
![Programme d’installation](./media/multi-factor-authentication-end-user-manage-myapps/proofup.png)
5. En haut, en regard de la vérification de sécurité supplémentaires, cliquez sur **les mots de passe app.**
6. Cliquez sur **créer**.
![Créer un mot de passe d’application](./media/multi-factor-authentication-end-user-app-passwords/create3.png)
7. Entrez un nom pour le mot de passe d’application et cliquez sur **suivant**.
![Créer un mot de passe d’application](./media/multi-factor-authentication-end-user-app-passwords/create1.png)
8. Copier le mot de passe d’application dans le Presse-papiers et les coller dans votre application.
![Créer un mot de passe d’application](./media/multi-factor-authentication-end-user-app-passwords/create2.png)

### <a name="to-delete-an-app-password-using-the-myapps-portal"></a>Pour supprimer un mot de passe de l’application via le portail Myapps

1. Ouvrez une session dans [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Dans la partie supérieure, sélectionnez le profil.
3. Sélectionnez la vérification de sécurité supplémentaires.
![Nuage](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. Ceci vous dirigera vers la page qui vous permet de modifier vos paramètres.
![Programme d’installation](./media/multi-factor-authentication-end-user-manage-myapps/proofup.png)
5. En haut, en regard de la vérification de sécurité supplémentaires, cliquez sur **les mots de passe app.**
6. En regard du mot de passe d’application que vous souhaitez supprimer, cliquez sur **Supprimer**.
![Supprimer un mot de passe d’application](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)
7. Cliquez sur **Oui**pour confirmer la suppression.
![Confirmer la suppression](./media/multi-factor-authentication-end-user-app-passwords/delete2.png)
8. Si vous supprimez le mot de passe d’application, vous pouvez cliquer sur **Fermer**.
![Fermer](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)


## <a name="create-app-passwords-in-the-azure-portal"></a>Créer des mots de passe app dans le portail Azure

Si vous utilisez une authentification multifactorielle avec Azure, vous souhaiterez créer des mots de passe app via le portail Azure.

### <a name="to-create-app-passwords-in-the-azure-portal"></a>Pour créer des mots de passe application dans Azure portal

1. Ouvrez une session dans le portail de gestion d’Azure.
2. En haut, avec le bouton droit sur votre nom d’utilisateur, puis sélectionnez Vérification de sécurité supplémentaire.
3. Sur la page proofup, en haut, sélectionnez les mots de passe app
4. Cliquez sur **créer**.
5. Entrez un nom pour le mot de passe d’application et cliquez sur **suivant**
6. Copier le mot de passe d’application dans le Presse-papiers et les coller dans votre application.
![Nuage](./media/multi-factor-authentication-end-user-app-passwords-create-azure/app2.png)

### <a name="to-delete-app-passwords-in-the-azure-portal"></a>Pour supprimer les mots de passe application dans Azure portal

1. Ouvrez une session dans le portail de gestion d’Azure.
2. En haut, avec le bouton droit sur votre nom d’utilisateur, puis sélectionnez Vérification de sécurité supplémentaire.
3. En haut, en regard de la vérification de sécurité supplémentaires, cliquez sur **les mots de passe app.**
4. En regard du mot de passe d’application que vous souhaitez supprimer, cliquez sur **Supprimer**.
5. Cliquez sur **Oui**pour confirmer la suppression.
6. Si vous supprimez le mot de passe d’application, vous pouvez cliquer sur **Fermer**.
![Fermer](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)
