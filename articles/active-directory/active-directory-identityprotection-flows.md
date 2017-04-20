<properties
    pageTitle="Connexion des expériences avec Protection d’identité de publicité Azure | Microsoft Azure"
    description="Fournit une vue d’ensemble de l’expérience utilisateur lors de la Protection d’identité a atténué ou convertis un utilisateur ou lorsqu’une authentification à plusieurs facteurs est requise par une stratégie."
    services="active-directory"
    keywords="protection d’identité Azure ActiveDirectory, découverte d’application cloud, gestion des applications, sécurité, risques, le niveau de risque, vulnérabilité, stratégie de sécurité"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/16/2016"
    ms.author="markvi"/>

# <a name="sign-in-experiences-with-azure-ad-identity-protection"></a>Connexion à des expériences avec Protection d’identité de publicité Azure

Avec Protection d’identité Azure Active Directory, vous pouvez :

- obliger les utilisateurs à s’inscrire pour plusieurs facteurs d’authentification

- gérer les connexions risquées et utilisateurs compromis

La réponse du système à ces problèmes a un impact sur l’expérience de connexion d’un utilisateur, car seulement directement la signature en fournissant un nom d’utilisateur et un mot de passe sera plus possible. Des étapes supplémentaires sont nécessaires pour obtenir un utilisateur en toute sécurité en entreprise.

Cette rubrique vous donne une vue d’ensemble de l’expérience de connexion d’un utilisateur dans tous les cas qui peuvent se produire.

**Plusieurs facteurs d’authentification**

- Enregistrement de l’authentification multicritères



**Reconnectez-vous à risque**

- Récupération de connexion risquée

- Risque reconnectez-vous bloqué

- Inscription de plusieurs facteurs d’authentification pendant un signe risqué
 

**Utilisateur à risque**

- Récupération du compte compromis

- Compte compromis bloqué




## <a name="multi-factor-authentication-registration"></a>Enregistrement de l’authentification multicritères

La meilleure expérience utilisateur dans les deux cas, le flux de récupération de compte compromis et le flux de connexion risqué, est lorsque l’utilisateur peut restaurer automatiquement. Si les utilisateurs sont enregistrés pour une authentification à facteurs multiples, ils ont déjà un numéro de téléphone associé à leur compte qui peut être utilisé pour passer des défis de sécurité. Sans intervention du bureau ou administrateur aide est nécessaire pour récupérer à partir de la compromission d’un compte. Par conséquent, il est fortement recommandé d’inscrit pour plusieurs facteurs d’authentification de vos utilisateurs. 

Les administrateurs peuvent :

- définir une stratégie qui impose aux utilisateurs de configurer leurs comptes pour la vérification de sécurité supplémentaires. 
- Permet d’ignorer l’enregistrement de l’authentification multicritères pendant 30 jours, dans le cas où ils souhaitent donner aux utilisateurs un délai de grâce avant d’enregistrer.

**L’enregistrement de l’authentification multicritères comporte trois étapes :**

1. Dans la première étape, l’utilisateur reçoit une notification de l’obligation de définir le compte pour l’authentification à plusieurs facteurs. 

    ![Mise à jour] (./media/active-directory-identityprotection-flows/140.png "Mise à jour")


2. Pour configurer l’authentification à facteurs multiples, vous devez informer le système comment vous souhaitez être contacté.

    ![Mise à jour] (./media/active-directory-identityprotection-flows/141.png "Mise à jour")
 
3. Le système envoie un défi pour vous et vous devez répondre.

    ![Mise à jour] (./media/active-directory-identityprotection-flows/142.png "Mise à jour")

 



## <a name="risky-sign-in-recovery"></a>Récupération de connexion risquée

Lorsqu’un administrateur a configuré une stratégie pour la connexion à des risques, les utilisateurs sont avertis lorsqu’ils essaient de se connecter. 

**Le flux de connexion risqué comporte deux étapes :** 

1. L’utilisateur est informé que quelque chose d’inhabituel a été détectée sur son signe, comme la signature un nouvel emplacement, un périphérique ou une application. 

    ![Mise à jour] (./media/active-directory-identityprotection-flows/120.png "Mise à jour")

2. L’utilisateur est tenu de prouver leur identité en résolvant un problème de sécurité. Si l’utilisateur est enregistré pour une authentification multifacteur ils doivent effectuer un aller-retour d’un code de sécurité à leur numéro de téléphone. Étant donné que ceci est seulement une risque de connexion et pas d’un compte compromis, l’utilisateur ne devra modifier le mot de passe dans ce flux. 

    ![Mise à jour] (./media/active-directory-identityprotection-flows/121.png "Mise à jour")



 
## <a name="risky-sign-in-blocked"></a>Risque reconnectez-vous bloqué
Les administrateurs peuvent également choisir de définir une stratégie d’ouverture de session risque pour empêcher les utilisateurs lors de l’authentification en fonction du niveau de risque. Pour obtenir débloqué, les utilisateurs finaux doivent contacter un administrateur ou le support technique, ou ils peuvent essayez de vous connecter à partir d’un emplacement de votre choix ou d’un périphérique. Récupération automatique en résolvant plusieurs facteurs d’authentification n’est pas une option dans ce cas.

![Mise à jour] (./media/active-directory-identityprotection-flows/200.png "Mise à jour")




## <a name="compromised-account-recovery"></a>Récupération du compte compromis

Lorsqu’une stratégie de sécurité d’utilisateur risque a été configurée, les utilisateurs qui répondent à l’utilisateur des risques au niveau spécifié dans la stratégie (et sont par conséquent supposé compromise) doivent passer par le flux de récupération utilisateur compromis avant qu’ils peuvent reconnectez-vous. 

**Le flux de récupération utilisateur compromis comporte trois étapes :**

1. L’utilisateur est informé que la sécurité de leur compte en raison d’une activité suspecte ou une fuite des informations d’identification.

    ![Mise à jour] (./media/active-directory-identityprotection-flows/101.png "Mise à jour")

2.  L’utilisateur est tenu de prouver leur identité en résolvant un problème de sécurité. Si l’utilisateur est enregistré pour une authentification multifacteur, ils peuvent automatiquement récupérer d’être compromis. Il faut effectuer un aller-retour d’un code de sécurité à leur numéro de téléphone. 

    ![Mise à jour] (./media/active-directory-identityprotection-flows/110.png "Mise à jour")


3.  Enfin, l’utilisateur est obligé de modifier leur mot de passe car une autre personne a pu avoir accès à leur compte. Captures d’écran de cette expérience sont inférieurs.
 
    ![Mise à jour] (./media/active-directory-identityprotection-flows/111.png "Mise à jour")



## <a name="compromised-account-blocked"></a>Compte compromis bloqué 

Pour obtenir un utilisateur qui a été bloqué par une stratégie de sécurité utilisateur risque débloquée, l’utilisateur doit contacter l’administrateur ou l’assistance technique. Récupération automatique en résolvant plusieurs facteurs d’authentification n’est pas une option dans ce cas.


![Mise à jour] (./media/active-directory-identityprotection-flows/104.png "Mise à jour")



 
## <a name="reset-password"></a>Réinitialiser le mot de passe

Si les utilisateurs compromis sont bloqués à partir de l’ouverture de session, un administrateur peut générer un mot de passe temporaire pour eux. Les utilisateurs doivent modifier leur mot de passe lors d’une prochaine connexion.

![Mise à jour] (./media/active-directory-identityprotection-flows/160.png "Mise à jour")


 




 

## <a name="see-also"></a>Voir aussi

- [Protection d’identité Azure Active Directory](active-directory-identityprotection.md) 