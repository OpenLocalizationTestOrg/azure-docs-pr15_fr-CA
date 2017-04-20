<properties
   pageTitle="Comment faire pour configurer des alertes de sécurité | Microsoft Azure"
   description="Apprenez à configurer les alertes de sécurité pour l’extension de la gestion des identités Azure privilégié."
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
   ms.date="09/02/2016"
   ms.author="kgremban"/>

# <a name="how-to-configure-security-alerts-in-azure-ad-privileged-identity-management"></a>Comment faire pour configurer des alertes de sécurité dans la gestion des identités Azure AD privilégié

## <a name="security-alerts"></a>Alertes de sécurité
Gestion d’identité privilégié Azure (PIM) génère des alertes en cas d’activité suspecte ou non sécurisée dans votre environnement. Lorsqu’une alerte est déclenchée, il s’affiche sur le tableau de bord PIM. Sélectionnez l’alerte pour afficher un rapport qui répertorie les utilisateurs ou les rôles qui a déclenché l’alerte.

![Alertes de sécurité du tableau de bord PIM - capture d’écran][1]



| Alerte | Déclencheur | Recommandation |
| ----- | ------- | -------------- |
| **Les rôles sont attribués à l’extérieur de PIM** | Un administrateur a été attribué de manière permanente à un rôle, en dehors de l’interface PIM. | Passez en revue la nouvelle attribution de rôle. Dans la mesure où les autres services ne peuvent affecter des administrateurs permanents, le modifier à une affectation éligible si nécessaire. |
| **Les rôles sont activés trop fréquemment.** | Il y avait trop de réactivations du même rôle dans les délais impartis dans les paramètres. | Contactez l’utilisateur pour savoir pourquoi ils ont activé le rôle autant de fois. La limite de temps est peut-être trop courte pour leur exécution de leurs tâches, ou peut-être qu’ils vous à l’aide de scripts afin d’activer automatiquement un rôle. |
| **Les rôles ne nécessitent pas une authentification multifactorielle pour l’activation** | Il existe des rôles sans AMF activé dans les paramètres. | Nous nécessitent AMF pour les rôles de privilèges plus élevés, mais encourageons vivement que vous activez AMF pour l’activation de tous les rôles. |
| **Les administrateurs ne sont pas à l’aide de leurs rôles privilégiés** | Sont éligibles aux administrateurs qui n’ont pas activé leur rôle récemment. | Démarrer une révision d’accès pour déterminer les utilisateurs qui ne doivent pas accéder plus. |
| **Il y a trop d’administrateurs globaux** | Il n’y a plus globales administrateurs à recommandées. | Si vous avez un nombre élevé d’administrateur global, il est probable que les utilisateurs obtiennent plus d’autorisations que nécessaire. Déplacer les utilisateurs vers les rôles moins privilégiées, ou certains d'entre eux faire éligibles pour le rôle au lieu d’attribuée de manière permanente. |

## <a name="configure-security-alert-settings"></a>Configurer les paramètres d’alerte sécurité

Vous pouvez personnaliser certaines des alertes de sécurité dans PIM pour fonctionner avec l’environnement et les objectifs de sécurité. Suivez ces étapes pour atteindre la lame de paramètres :

1. Connectez-vous au [portail Azure](https://portal.azure.com/) et sélectionnez la mosaïque de **la gestion des identités Azure AD privilégié** du tableau de bord.
2. Sélectionner **les rôles privilégiés de gestion** > **paramètres** > **les paramètres d’alerte**.

    ![Accédez aux paramètres des alertes de sécurité][2]

### <a name="roles-are-being-activated-too-frequently-alert"></a>Alerte de « Rôles sont en cours d’activation trop fréquemment »

Cette alerte se déclenche si un utilisateur Active le rôle de privilège de même plusieurs fois dans une période spécifiée. Vous pouvez configurer la période de temps et le nombre d’activations.

- **Période de renouvellement de l’activation**: spécifier en jours, heures, minutes et la seconde de la période que vous souhaitez utiliser pour effectuer le suivi des renouvellements suspects.

- **Nombre de renouvellements de l’activation**: spécifiez le nombre d’activations, de 2 à 100, ce que vous considérez comme digne d’alerte, dans le délai que vous avez choisi. Vous pouvez modifier ce paramètre en déplaçant le curseur ou en tapant un nombre dans la zone de texte.


### <a name="there-are-too-many-global-administrators-alert"></a>« Il existe trop d’administrateurs globaux » l’alerte

PIM déclenche cette alerte si deux différentes conditions, et vous pouvez configurer les deux. Vous devez tout d’abord, atteignent un certain seuil d’administrateurs globaux. En second lieu, un certain pourcentage votre total des affectations de rôle doit être un administrateur global. Si vous seulement répondent à l’une de ces mesures, l’alerte ne s’affiche pas.  

- **Nombre minimal d’administrateurs globaux**: spécifiez le nombre d’administrateurs globaux, à partir de 2 à 100, que vous considérez comme un montant non sécurisé.

- **Pourcentage des administrateurs globaux**: spécifier le pourcentage d’administrateurs sont des administrateurs globaux, à partir de 0 % à 100 %, qui est potentiellement dangereux dans votre environnement.

### <a name="administrators-arent-using-their-privileged-roles-alert"></a>« Les administrateurs ne sont pas à l’aide de leurs rôles privilégiés » l’alerte

Cette alerte se déclenche si un utilisateur passe un certain laps de temps sans l’activer un rôle.

- **Nombre de jours**: spécifiez le nombre de jours, de 0 à 100, sans avoir à activer un rôle d’un utilisateur.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]


<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-configure-security-alerts/PIM_security_dash.png
[2]: ./media/active-directory-privileged-identity-management-how-to-configure-security-alerts/PIM_security_settings.png
