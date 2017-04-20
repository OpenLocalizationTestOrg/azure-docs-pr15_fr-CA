<properties
    pageTitle="Forum aux questions : Mot de passe Active Directory Azure Management | Microsoft Azure"
    description="Forum aux questions (FAQ) sur la gestion de mot de passe dans Active Directory Azure, y compris mot de passe réinitialisé, inscription, rapports et écriture différée sur site Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="asteen"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/12/2016"
    ms.author="asteen"/>

# <a name="password-management-frequently-asked-questions"></a>Questions fréquentes sur la gestion de mot de passe

> [AZURE.IMPORTANT] **Vous êtes ici car vous rencontrez des problèmes de connexion ?** Si tel est le cas, [Voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md).

Voici quelques questions fréquemment posées pour toutes les tâches associées à la gestion de mot de passe.

Si vous vous trouvez à une question que vous ne connaissez pas la réponse à, ou recherchez un problème spécifique, vous êtes confronté à l’aide, vous pouvez lire sur ci-dessous pour voir si nous l’avons traité déjà.  Si nous n’avez pas déjà, ne vous inquiétez pas ! N’hésitez pas à poser des questions qui n’est pas couverte ici sur les [Forums de publicité Azure](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD) et nous vous répondrons dès vous dès que possible.

Ce forum aux questions est divisée dans les sections suivantes :

- [**Questions sur l’inscription de réinitialisation de mot de passe**](#password-reset-registration)
- [**Questions à propos de la réinitialisation de mot de passe**](#password-reset)
- [**Questions sur les rapports de gestion de mot de passe**](#password-management-reports)
- [**Questions à propos de l’écriture différée de mot de passe**](#password-writeback)

## <a name="password-reset-registration"></a>L’inscription de réinitialisation de mot de passe
 - **Q : Mes utilisateurs permettra de leurs propres données de réinitialisation de mot de passe ?**

 > **A:** Oui, tant que la réinitialisation de mot de passe est activée et qu’ils sont sous licence, ils peuvent atteindre le portail de l’enregistrement de réinitialisation de mot de passe à l’http://aka.ms/ssprsetup d’enregistrer leurs informations d’authentification à utiliser avec la réinitialisation de mot de passe. Les utilisateurs peuvent également enregistrer en allant dans le panneau d’accès à http://myapps.microsoft.com, cliquez sur l’onglet profil et sur le Registre pour l’option de réinitialisation de mot de passe. Pour en savoir plus sur comment configuré pour réinitialiser en lisant comment obtenir les utilisateurs configurés pour la réinitialisation de mot de passe mot de passe de vos utilisateurs.

 - **Q : puis-je définir données de réinitialisation de mot de passe pour le compte de mes utilisateurs ?**

 > **A:** Oui, vous pouvez le faire avec la synchronisation d’annuaire ou de PowerShell, ou via le portail [Azure portail de gestion](https://manage.windowsazure.com) ou d’administration d’Office. En savoir plus sur cette fonctionnalité sur le billet de blog confidentialité améliorée pour Azure AD AMF et numéros de téléphone de réinitialisation de mot de passe et en lisant apprendre comment les données sont utilisées par mot de passe est réinitialisé.

 - **Q : est-il possible de synchroniser des données pour des questions de sécurité dans les locaux ?**

 > **A:** Non, ce n’est pas possible aujourd'hui, mais nous envisageons d’elle.

 - **Q : Mes utilisateurs peuvent enregistrer des données de manière à ce que les autres utilisateurs ne peuvent pas afficher ces données ?**

 > **A:** Oui, lorsque les utilisateurs enregistrent des données en utilisant le portail de l’enregistrement de réinitialisation de mot de passe il est enregistré dans des champs de l’authentification privé qui ne sont visibles que par les administrateurs globaux et l’utilisateur lui-même. En savoir plus sur cette fonctionnalité sur le billet de blog confidentialité améliorée pour Azure AD AMF et numéros de téléphone de réinitialisation de mot de passe et en lisant apprendre comment les données sont utilisées par mot de passe est réinitialisé.

 - **Q : Mes utilisateurs doivent-ils être enregistrés avant de pouvoir utiliser la réinitialisation de mot de passe ?**

 > **A:** Non, si vous définissez suffisamment d’informations d’authentification en leur nom, les utilisateurs n’auront pas à inscrire. Réinitialisation de mot de passe fonctionnent uniquement dans la mesure où vous avez correctement mise en forme des données stockées dans les champs appropriés dans le répertoire. En savoir que plus sur en lisant apprendre comment les données sont utilisées par la réinitialisation du mot de passe.

 - **Q : puis-je synchroniser ou définir les champs de l’authentification E-mail ou téléphone d’authentification, le téléphone de l’authentification pour le compte de mes utilisateurs ?**

 > **A:** Pas actuellement, mais nous envisageons l’activation de cette fonctionnalité.

 - **Q : comment le portail d’inscription que les options à afficher mes utilisateurs ?**

 > **A:** Le portail de l’enregistrement de réinitialisation de mot de passe affiche uniquement les options que vous avez activé pour vos utilisateurs dans la section stratégie de réinitialisation de mot de passe utilisateur de l’onglet de configuration de votre annuaire. Cela signifie que si vous n’activez pas, par exemple, les questions de sécurité, alors les utilisateurs sera pas en mesure d’inscrire pour cette option.

 - **Q : quand un utilisateur est considéré enregistré ?**

 > **A:** Un utilisateur est considéré comme inscrit lorsque qu’il a au moins les éléments N des informations d’authentification défini, où N est le nombre de d’authentification méthodes requises que vous avez définies dans le [Portail de gestion Azure](https://manage.windowsazure.com). Pour plus d’informations, consultez Personnalisation de la stratégie de réinitialisation d’utilisateur mot de passe.


## <a name="password-reset"></a>Réinitialisation du mot de passe

 - **Q : combien de temps faut-il attendre pour recevoir un courrier électronique, SMS ou appel téléphonique à partir de réinitialisation de mot de passe ?**

 > **A:** E-mails, messages SMS et appels téléphoniques devrait vous parvenir dans moins de 1 minute, avec le cas normal, qui est de 5 à 20 secondes. Si vous ne recevez pas la notification dans ce délai, vérifiez votre dossier courrier indésirable, que le numéro de messagerie contacté est celle que vous soupçonnez, et que les données d’authentification dans le répertoire sont correctement mis en forme. Pour plus d’informations sur la mise en forme des numéros de téléphone et courrier électronique adresses pour une utilisation avec la réinitialisation de mot de passe voir apprendre comment les données sont utilisées par la réinitialisation du mot de passe.

 - **Q : quelles sont les langues sont prises en charge par la réinitialisation du mot de passe ?**

 > **A:** Le mot de passe réinitialiser l’interface utilisateur, les messages SMS et appels vocaux sont localisées dans les mêmes 40 langues prises en charge dans Office 365. Ce sont : arabe, bulgare, chinois simplifié, chinois traditionnel, croate, tchèque, danois, néerlandais, anglais, estonien, finnois, Français, allemand, grec, hébreu, Hindi, hongrois, Indonésien, italien, japonais, Kazakh, coréen, letton, lituanien, Malais (Malaisie), norvégien (Bokmål), polonais, portugais (Brésil), portugais (Portugal), roumain, russe, serbe (Latin), slovaque, slovène, espagnol, suédois, thaï, turc, ukrainien et vietnamien.

 - **Q : quelles parties de l’expérience de réinitialisation de mot de passe obtenir marque lors de la définition d’organisation personnalisation dans mon répertoire de configuration onglet ?**

 > **A:** Le portail de réinitialisation de mot de passe affiche le logo de votre organisation et vous permet aussi de configurer le Contact votre lien administrateur pointer vers une URL ou un e-mail personnalisé. Tout e-mail envoyé par réinit. inclura le logo de votre entreprise, couleurs (rouges dans ce cas), nom dans le corps de l’e-mail et personnalisés à partir du nom. Voir un exemple avec tous les éléments marques ci-dessous. Pour plus d’informations, lire la réinitialisation de mot de passe de personnalisation apparence.

  ![][001]

 - **Q : Comment puis-je expliquer à mes utilisateurs où aller pour réinitialiser leurs mots de passe ?**

 > **A:** Vous pouvez envoyer directement à vos utilisateurs d’https://passwordreset.microsoftonline.com, ou vous pouvez leur demander de cliquer sur le lien avec votre compte trouvé sur n’importe quel signe de l’école ou de l’ID de travail dans l’écran ne peut pas accéder à. Vous pouvez n’hésitez pas à publier ces liens (ou créer les redirections d’URL) dans un lieu qui est facilement accessible à vos utilisateurs.

 - **Q : puis-je utiliser cette page à partir d’un périphérique mobile ?**

 > **A:** Oui, cette page fonctionne sur les périphériques mobiles.

 - **Q : ne vous prennent en charge les comptes active directory local déverrouillage lorsque les utilisateurs réinitialiser leur mot de passe ?**

 > **A:** Oui, la lorsqu’un utilisateur réinitialise son mot de passe et l’écriture différée de mot de passe a été déployée avec toutes les versions d’Azure AD Connect ou versions d’Azure AD Sync 1.0.0485.0222 ou version ultérieure, puis le compte de cet utilisateur sera déverrouillé automatiquement lorsque l’utilisateur réinitialise son mot de passe.

 - **Q : Comment puis-je intégrer directement dans mon bureau connexion utilisateur de réinitialisation de mot de passe ?**

 > **A:** Ce n’est pas possible aujourd'hui. Toutefois, si absolument, vous avez besoin de cette fonctionnalité et que vous êtes un client Premium de publicité Azure, vous pouvez installer le Gestionnaire d’identité Microsoft sans coût supplémentaire et déployer la solution de réinitialisation de mot de passe local trouvée pour résoudre cette condition.

 - **Q : est-il possible de définir des questions de sécurité différentes pour différents paramètres régionaux ?**

 > **A:** Non, ce n’est pas possible aujourd'hui, mais nous envisageons d’elle.

 - **Q : combien de questions nous pouvons configurer pour l’option d’authentification des Questions de sécurité ?**

 > **A:** Vous pouvez configurer jusqu'à 20 questions de sécurité personnalisés dans le [Portail de gestion Azure](https://manage.windowsazure.com).

 - **Q : combien de temps les questions de sécurité peuvent être ?**

 > **A:** Les questions de sécurité peuvent être comprise entre 3 et 200 caractères.

 - **Q : combien de temps peuvent être les réponses aux questions de sécurité ?**

 > **A:** Réponses peuvent être de 3 à 40 caractères.

 - **Q : sont en double des réponses à des questions de sécurité rejetées ?**

 > **A:** Oui, nous rejeter en double réponses à des questions de sécurité.

 - **Q : mai un utilisateur enregistrer plusieurs fois la même question de sécurité ?**

 > **A:** Non, une fois qu’un utilisateur s’inscrit à une question particulière, il ou elle peut inscrire pas de cette question une deuxième fois.

 - **Q : est-il possible de définir une limite minimale de questions de sécurité pour l’enregistrement et réinitialiser ?**

 > **A:** Oui, une limite peut être définie pour l’enregistrement et un autre pour la réinitialisation. les questions de sécurité de 3 à 5 peuvent être requises pour l’inscription et 3 à 5 peuvent être requis pour la réinitialisation.

 - **Q : si un utilisateur a enregistré plus que le nombre maximal de questions requises pour réinitialiser, comment se sont les questions de sécurité sélectionnées lors de la réinitialisation ?**

 > **A:** Questions de sécurité N sont sélectionnées au hasard sur le nombre total de questions de qu'un utilisateur est enregistrée, où N est le nombre minimal de questions nécessaires à la réinitialisation de mot de passe. Par exemple, si un utilisateur a enregistré 5 questions de sécurité, mais seuls 3 sont requis pour réinitialiser, 3 de ces 5 sera sélectionnée de manière aléatoire et présentés à l’utilisateur au moment de la réinitialisation. Si l’utilisateur obtient les réponses aux questions est incorrect, le processus de sélection se reproduit pour empêcher de martelage de la question.

 - **Q : empêcher les utilisateurs d’essayer plusieurs fois dans une période courte de réinitialisation de mot de passe ?**

 > **A:** Il existe plusieurs fonctionnalités de sécurité intégrées de réinitialisation de mot de passe. Les utilisateurs peuvent essayer uniquement de 5 tentatives de réinitialisation de mot de passe en une heure avant d’être verrouillé pendant 24 heures. Les utilisateurs peuvent uniquement essaie de valider un numéro de téléphone 5 fois en une heure avant d’être verrouillé pendant 24 heures. Les utilisateurs peuvent essayer uniquement d’une méthode d’authentification unique 5 fois en une heure avant d’être verrouillé pendant 24 heures.

 - **Q : de combien de temps les e-mail et SMS unique mot de passe valide ?**

 > **A:** La durée de la session de réinitialisation de mot de passe est de 105 minutes. Cela signifie que, à partir du début du mot de passe, réinitialisation, l’utilisateur a 105 minutes pour réinitialiser son mot de passe. Le courrier électronique et le code unique de SMS ne sont pas valides après l’expiration de cette période.


## <a name="password-management-reports"></a>Rapports de gestion de mot de passe

 - **Q : combien de temps faut-il pour les données à afficher sur les rapports de gestion de mot de passe ?**

 > **A:** Données doivent apparaître dans les rapports de gestion de mot de passe dans les 5 à 10 minutes. Il parfois peut prendre jusqu'à une heure s’affiche.

 - **Q : Comment puis-je filtrer les rapports de gestion de mot de passe ?**

 > **A:** Vous pouvez filtrer les rapports de gestion de mot de passe en cliquant sur la petite loupe à l’extrême droite des étiquettes de colonne, en haut du rapport (voir la capture d’écran). Si vous souhaitez effectuer un filtrage plus riche, vous pouvez télécharger le rapport dans excel et créer un tableau croisé dynamique.

  ![][002]

 - **Q : quel est le nombre maximal d’événements sont stockés dans les rapports de gestion de mot de passe ?**

 > **A:** Jusqu'à 1 000 de mot de passe mot de passe ou de réinitialisation réinitialisation les événements d’enregistrement sont stockées dans les rapports de gestion de mot de passe.  Nous nous efforçons d’augmenter ce nombre pour inclure davantage d’événements.

 - **Q : combien de nouveau faire les rapports de gestion de mot de passe ?**

 > **A:** Les rapports de gestion de mot de passe affichent les opérations qui se produisent dans les 30 derniers jours. Nous étudions actuellement comment transformer une période plus longue. Pour le moment, si vous avez besoin pour l’archivage de ces données, vous pouvez télécharger régulièrement les rapports et les enregistrer dans un emplacement distinct.

 - **Q : y a-t-il un nombre maximal de lignes à afficher dans les rapports de gestion de mot de passe ?**

 > **A:** Oui, un maximum de 1 000 lignes peut-être apparaître sur un des rapports de gestion de mot de passe, si elles sont en cours affichés dans l’interface utilisateur ou en cours de téléchargement. Nous étudions actuellement comment augmenter cette limite.

 - **Q : y a-t-il une API pour accéder à la réinitialisation de mot de passe ou l’enregistrement de données de rapports ?**

 > **A:** Oui, reportez-vous à la documentation suivante pour savoir comment vous pouvez accéder à la réinitialisation de mot de passe des flux de données de création de rapports.  [Découvrez comment accéder aux mot de passe réinitialiser des événements par programme](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent).

## <a name="password-writeback"></a>Écriture différée de mot de passe
 - **Q : comment l’écriture différée de mot de passe ne fonctionne pas en arrière-plan ?**

 > **A:** Pour une explication détaillée de ce qui se passe lorsque vous activez l’écriture différée de mot de passe, ainsi que la manière dont les données circulent dans le système dans votre environnement local, consultez [que fonctionne de la manière dont l’écriture différée de mot de passe](active-directory-passwords-learn-more.md#how-password-writeback-works) . Voir [modèle de sécurité de mot de passe d’écriture en différé](active-directory-passwords-learn-more.md#password-writeback-security-model) dans l’écriture différée de mot de passe fonctionne pour savoir comment nous assurer que l’écriture différée de mot de passe est un service hautement sécurisé.

 - **Q : combien de temps l’écriture différée de mot de passe faut-il pour travailler ?  Y a-t-il un retard de synchronisation comme avec synchronisation de hachage de mot de passe ?**

 > **A:** L’écriture différée de mot de passe est instantanée. Il s’agit d’un pipeline synchrone qui fonctionne fondamentalement différente de celle de synchronisation de hachage de mot de passe. L’écriture différée de mot de passe permet aux utilisateurs d’obtenir des commentaires en temps réel sur la réussite de leur réinitialisation de mot de passe ou l’opération de modification. La durée moyenne d’une écriture différée de réussi d’un mot de passe est de moins de 500 ms.

 - **Q : quels sont les types de comptes d’écriture différée de mot de passe fonctionne pour ?**

 > **A:** L’écriture différée de mot de passe fonctionne pour fédéré et synchronisation de hachage de mot de passe d utilisateurs.

 - **Q : l’écriture différée de mot de passe n’applique les stratégies de mot de passe de mon domaine ?**

 > **A:** Oui, l’écriture différée de mot de passe applique l’âge du mot de passe, l’historique, la complexité, les filtres et toute autre restriction que vous pouvez mettre en place de mots de passe de votre domaine local.

 - **Q : est différée de mot de passe sécurisée ?  Comment puis-je être sûr que je ne de pirater ?**

 > **A:** Oui, l’écriture différée de mot de passe est extrêmement sécurisée. Pour en savoir plus sur les 4 niveaux de sécurité implémenté par le service de l’écriture différée de mot de passe, consultez le [modèle de sécurité de mot de passe d’écriture en différé](active-directory-passwords-learn-more.md#password-writeback-security-model) dans fonctionne de la manière dont l’écriture différée de mot de passe.




## <a name="links-to-password-reset-documentation"></a>Des liens vers le mot de passe réinitialiser la documentation
Vous trouverez ci-dessous des liens vers toutes les pages de documentation de réinitialisation de mot de passe Active Directory Azure :

* **Vous êtes ici car vous rencontrez des problèmes de connexion ?** Si tel est le cas, [Voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md).
* [**Mode de fonctionnement**](active-directory-passwords-how-it-works.md) - en savoir plus sur les différents composants de service et de six chacune d’entre-elles
* [**Mise en route**](active-directory-passwords-getting-started.md) - Apprenez à vous permettent aux utilisateurs de réinitialiser et de modifier leurs mots de passe nuage ou sur site
* [**Personnaliser**](active-directory-passwords-customize.md) - Apprenez à personnaliser l’aspect et la convivialité et le comportement du service pour les besoins de votre organisation
* [**Meilleures pratiques**](active-directory-passwords-best-practices.md) - Apprenez à déployer rapidement et efficacement gérer les mots de passe dans votre organisation
* [**Se référant**](active-directory-passwords-get-insights.md) - en savoir plus sur nos capacités de création de rapports intégrées
* [**Dépannage**](active-directory-passwords-troubleshoot.md) - Apprenez à rapidement résoudre les problèmes avec le service
* [**En savoir plus**](active-directory-passwords-learn-more.md) - profondeur accédez aux détails techniques du fonctionne du service


[001]: ./media/active-directory-passwords-faq/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-faq/002.jpg "Image_002.jpg"
