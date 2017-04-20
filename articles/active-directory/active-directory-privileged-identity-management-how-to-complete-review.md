<properties
   pageTitle="Comment effectuer une révision d’accès | Microsoft Azure"
   description="Une fois que vous avez démarré une révision d’accès dans la gestion des identités Azure AD privilégié, savoir comment réaliser et afficher les résultats"
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/30/2016"
   ms.author="kgremban"/>

# <a name="how-to-complete-an-access-review-in-azure-ad-privileged-identity-management"></a>Comment effectuer un examen de l’accès dans la gestion des identités Azure AD privilégié


Rôle privilégié les administrateurs peuvent étudier un accès privilégié qu’une seule fois une [révision de sécurité a été démarrée](active-directory-privileged-identity-management-how-to-start-security-review.md). Azure privilégié d’Active Directory Identity Management (PIM) envoie automatiquement un message invitant les utilisateurs à consulter leur accès. Si un utilisateur n’a pas reçu un message électronique, vous pouvez leur envoyer des instructions comment [effectuer une révision de sécurité](active-directory-privileged-identity-management-how-to-perform-security-review.md).

Après la période de révision de sécurité, ou tous les utilisateurs ont terminé leur vérifier automatiquement, suivez les étapes décrites dans cet article pour gérer la révision et afficher les résultats.

## <a name="manage-security-reviews"></a>Gérer les révisions de sécurité

1. Accédez au [portail Azure](https://portal.azure.com/) et sélectionnez l’application de **Gestion des identités Azure AD privilégié** sur votre tableau de bord.
2. Sélectionnez la section **accès révisions** du tableau de bord.
3. Sélectionnez la révision d’accès que vous souhaitez gérer.

Sur la lame de détail de la révision de l’accès, il existe un nombre options pour la gestion de ce réexamen.

![Boutons de révision accès PIM - capture d’écran][1]

### <a name="remind"></a>Rappeler

Si un contrôle d’accès est configuré afin que les utilisateurs vérifier eux-mêmes, le bouton **rappeler** envoie une notification. 

### <a name="stop"></a>Arrêter

Tous les accès révisions ont une date de fin, mais vous pouvez utiliser le bouton **Arrêter** pour terminer dès le début. Si les utilisateurs n’ont pas été révisées en ce moment, ils ne pourront après avoir arrêté la révision. Vous ne pouvez pas redémarrer une révision après son arrêt.

### <a name="apply"></a>Appliquer

Après un examen de l’accès, soit parce que vous atteint sa date de fin ou s’est arrêté manuellement, le bouton **Appliquer** implémente le résultat de l’examen. Si accès un utilisateur l’a été refusé lors de la révision, il s’agit de l’étape qui va supprimer leur affectation de rôle.  

### <a name="export"></a>Exporter

Si vous souhaitez appliquer les résultats de la révision de sécurité manuellement, vous pouvez exporter la révision. Le bouton **Exporter** démarrera le téléchargement d’un fichier CSV. Vous pouvez gérer les résultats dans Excel ou d’autres programmes qui ouvrent des fichiers CSV.

### <a name="delete"></a>Supprimer

Si vous n’êtes pas intéressé par la révision de toute autre, supprimez-le. Le bouton **Supprimer** supprime la révision de l’application PIM.

> [AZURE.IMPORTANT] Vous ne sera pas obtenir un message d’avertissement avant que la suppression se produit, assurez-vous que vous souhaitez supprimer ce réexamen.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]


<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-complete-review/PIM_review_buttons.png
