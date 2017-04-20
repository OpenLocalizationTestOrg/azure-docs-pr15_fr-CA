<properties
    pageTitle="Notifications de rapport Azure Active Directory"
    description="L’utilisation de l’Active Directory Azure reporting des notifications pour suspect ins."
    services="active-directory"
    documentationCenter=""
    authors="dhanyahk"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/07/2016"
    ms.author="dhanyahk"/>

# <a name="azure-active-directory-reporting-notifications"></a>Notifications de rapport Azure Active Directory

## <a name="what-reports-generate-email-notifications"></a>Quels rapports générant des notifications par courrier électronique

À ce stade, seul le signe irrégulière dans les déclencheurs de rapport d’activité des notifications par e-mail.

## <a name="what-is-an-irregular-sign-in"></a>Quel est le « Signe irrégulière dans » ?

Connexions irréguliers sont ceux qui ont été identifiés par nos algorithmes, sur la base d’une condition de « voyage impossible » combiné avec un emplacement de connexion anormale et un dispositif de formation de machine. Cela peut indiquer qu’un pirate informatique a tenté de se connecter à l’aide de ce compte.

## <a name="who-receives-the-email-notifications"></a>Qui reçoit les notifications par courrier électronique ?

Le courrier électronique est envoyé à tous les administrateurs globaux qui possèdent une licence Active Directory prime. Pour vous assurer qu’il est livré, nous l’envoyer pour les administrateurs autre adresse E-mail. Administrateurs doit inclure aad-alerts-noreply@mail.windowsazure.com dans leur liste d’expéditeurs fiables afin qu’ils ne manquez pas le courrier électronique.

## <a name="how-often-are-these-emails-sent"></a>La fréquence à laquelle sont ces courriels envoyés ?

Le courrier électronique est envoyé si 10 nouvelles irrégulières reconnectez-vous activités se produisent au cours des 30 derniers jours, ou depuis le dernier e-mail a été envoyé, si elle est inférieure.

## <a name="how-do-i-access-the-report-mentioned-in-the-email"></a>Comment accéder à l’état mentionné dans le courrier électronique ?

Lorsque vous cliquez sur le lien, vous êtes redirigé vers la page de rapport au sein du portail classique Azure. Pour accéder au rapport, vous devez être à la fois :

- Un administrateur ou un co-admin de votre abonnement Azure

- Un administrateur global dans l’annuaire et attribué une licence Active Directory prime. Pour plus d’informations, consultez [éditions d’Azure Active Directory](active-directory-editions.md).

## <a name="can-i-turn-off-these-emails"></a>Puis-je désactiver ces e-mails ?

Oui, pour désactiver les notifications relatives aux connexions anormales dans Azure portal classique, cliquez sur **configurer**et sélectionnez **désactivé** dans la section **Notifications** .

## <a name="whats-next"></a>Quel est l’avenir
- Curieux de savoir sur la sécurité, d’audit et rapports sur les activités ne sont disponibles ? Extraire [Azure AD sécurité, Audit et rapports sur les activités](active-directory-view-access-usage-reports.md)
- [Mise en route avec Azure Active Directory prime](active-directory-get-started-premium.md)
- [Ajouter des pages de personnalisation et votre connexion d’accès de société](active-directory-add-company-branding.md)
