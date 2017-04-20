<properties 
    pageTitle="Rapports d’Azure, plusieurs facteurs d’authentification"
    description="Il décrit comment modifier les paramètres utilisateurs tels que d’obliger les utilisateurs à faire preuve des processus à nouveau."
    documentationCenter=""
    services="multi-factor-authentication"
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

# <a name="managing-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Gestion des paramètres utilisateur avec authentification à plusieurs facteurs Azure dans le nuage

En tant qu’administrateur, vous pouvez gérer les paramètres utilisateur et de périphérique.  

- [Les utilisateurs sélectionnés fournir à nouveau les méthodes de contact](#require-selected-users-to-provide-contact-methods-again)
- [Supprimer des utilisateurs existants de mots de passe app](#delete-users-existing-app-passwords)
- [Restauration AMF sur tous les périphériques de suspens pour un utilisateur](#restore-mfa-on-all-suspended-devices-for-a-user)






Ceci est utile si un ordinateur ou un périphérique est perdu ou volé ou si vous devez supprimer un accès aux utilisateurs.


## <a name="require-selected-users-to-provide-contact-methods-again"></a>Les utilisateurs sélectionnés fournir à nouveau les méthodes de contact

Ce sera de paramètre force l’utilisateur à réexécuter le processus d’inscription lorsqu’il ou elle se connecte. N’oubliez pas que les applications autres que des navigateurs continue de fonctionner si l’utilisateur dispose de mots de passe app pour eux.  Vous pouvez supprimer les mots de passe utilisateurs app en choisissant **Supprimer tous les mots d’application existantes générées par les utilisateurs sélectionnés**.

### <a name="how-to-require-users-to-provide-contact-methods-again"></a>Comment les utilisateurs doivent fournir à nouveau les méthodes de contact




1. Ouvrez une session dans le portail classique Azure.
2. Sur la gauche, cliquez sur Active Directory.
3. Sous, répertoire cliquez sur le répertoire de l’utilisateur que vous voulez fournir leur méthode de contact à nouveau.
4. En haut, cliquez sur utilisateurs.
5. En bas de la page, cliquez sur Gérer l’AUTH multi-facteurs. Cela ouvre la page d’authentification à plusieurs facteurs.
6. Trouver l’utilisateur que vous souhaitez gérer et placez une coche dans la case située en regard du nom. Vous devrez peut-être modifier l’affichage située en haut.
7. Le lien **Gérer les paramètres utilisateur** à droite s’affiche. Cliquez dessus.
8. Activez **les utilisateurs sélectionnés fournir des méthodes de contact à nouveau**.
![Fournir des méthodes de contact](./media/multi-factor-authentication-manage-users-and-devices/reproofup.png)
10. Cliquez sur Enregistrer.
11. Cliquez sur Fermer

## <a name="delete-users-existing-app-passwords"></a>Supprimer des utilisateurs existants de mots de passe app

Cette opération supprime tous les mots de passe d’application qui les a créés par un utilisateur. Les applications non-navigateur qui ont été associées à ces mots de passe d’application seront cessent de fonctionner jusqu'à ce qu’un nouveau mot de passe d’application est créé.

### <a name="how-to-delete-users-existing-app-passwords"></a>Comment faire pour supprimer des utilisateurs existants de mots de passe app

1. Ouvrez une session dans le portail classique Azure.
2. Sur la gauche, cliquez sur Active Directory.
3. Sous, répertoire cliquez sur le répertoire de l’utilisateur que vous souhaitez supprimer les mots de passe app pour.
4. En haut, cliquez sur utilisateurs.
5. En bas de la page, cliquez sur Gérer l’AUTH multi-facteurs. Cela ouvre la page d’authentification à plusieurs facteurs.
6. Trouver l’utilisateur que vous souhaitez gérer et placez une coche dans la case située en regard du nom. Vous devrez peut-être modifier l’affichage située en haut.
7. Le lien **Gérer les paramètres utilisateur** à droite s’affiche. Cliquez dessus.
8. Placez une coche dans la zone **Supprimer tous les mots d’application existantes générées par les utilisateurs sélectionnés**.
![Supprimer les mots de passe app](./media/multi-factor-authentication-manage-users-and-devices/deleteapppasswords.png)
10. Cliquez sur Enregistrer.
10. Cliquez sur Fermer.

## <a name="restore-mfa-on-all-remembered-devices-for-a-user"></a>Restauration AMF sur tous les périphériques à retenir pour un utilisateur

Les administrateurs ont la possibilité de restaurer une authentification multifactorielle sur les navigateurs et les périphériques des utilisateurs. Lors de cette opération, cela supprimera la mémoriser AMF de tous les périphériques de l’utilisateur et navigateurs et l’utilisateur devra utiliser AMF lors de la prochaine ouverture de session.

### <a name="how-to-restore-mfa-on-all-suspended-devices-for-a-user"></a>Comment faire pour restaurer AMF sur tous les périphériques de suspens pour un utilisateur

1. Ouvrez une session dans le portail classique Azure.
2. Sur la gauche, cliquez sur Active Directory.
3. Sous, répertoire cliquez sur le répertoire de l’utilisateur que vous souhaitez restaurer AMF sur.
4. En haut, cliquez sur utilisateurs.
5. En bas de la page, cliquez sur Gérer l’AUTH multi-facteurs. Cela ouvre la page d’authentification à plusieurs facteurs.
6. Trouver l’utilisateur que vous souhaitez gérer et placez une coche dans la case située en regard du nom. Vous devrez peut-être modifier l’affichage située en haut.
7. Le lien **Gérer les paramètres utilisateur** à droite s’affiche. Cliquez dessus.
8. Placez une coche dans la **restauration d’une authentification multifactorielle sur tous les périphériques mémorisées**
![supprimer les mots de passe app](./media/multi-factor-authentication-manage-users-and-devices/rememberdevices.png)
9. Cliquez sur Enregistrer.
10. Cliquez sur Fermer.
