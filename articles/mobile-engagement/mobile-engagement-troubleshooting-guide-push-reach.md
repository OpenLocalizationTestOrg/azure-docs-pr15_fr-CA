<properties 
   pageTitle="Engagement de Mobile Azure Guide - Push/portée de dépannage" 
   description="Résolution des problèmes de notification et d’interaction utilisateur Mobile dans Azure" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="08/19/2016"
   ms.author="piyushjo"/>

# <a name="troubleshooting-guide-for-push-and-reach-issues"></a>Guide de dépannage pour pousser et atteindre des problèmes

Les éléments suivants sont des problèmes possibles que vous pouvez rencontrer avec comment Azure Mobile Engagement envoie des informations à vos utilisateurs.
 
## <a name="push-failures"></a>Échecs de transmission

### <a name="issue"></a>Problème
- Push ne fonctionne pas (dans l’application, de l’application, ou les deux).

### <a name="causes"></a>Causes
- Un échec de transmission souvent est une indication que Azure Mobile Engagement, portée ou une autre fonctionnalité avancée d’Azure Mobile Engagement n’est pas correctement intégrée ou qu’une mise à niveau est requis dans le Kit de développement pour résoudre un problème connu avec une nouvelle plate-forme de système d’exploitation ou un périphérique.
- Tester simplement un push de l’application et simplement un push de l’application pour déterminer si un élément est un problème dans l’application ou de l’application.
- Tester à partir de l’interface utilisateur et de l’API comme étape de dépannage pour afficher les informations d’erreur supplémentaires sont disponibles aux deux endroits.
- En dehors de l’application push ne fonctionne pas si l’Engagement de Mobile Azure et la portée sont intégrés dans le Kit de développement.
- Push ne fonctionne pas si les certificats ne sont pas valides ou sont à l’aide de nomenclature de production et DEV correctement (e/s uniquement). (**Remarque :** insuffisante"app » notifications de type Pousser ne soient pas remises pour iOS, si vous avez à la fois le développement (DEV) et des versions de production (PROD) de votre application installé sur le même périphérique, dans la mesure où le jeton de sécurité associée à votre certificat ne peuvent être invalidées par Apple. Pour résoudre ce problème, désinstallez les versions nomenclature de production et de développement de votre application et ré-installez uniquement la version sur votre périphérique.)
- Hors émission d’application nombres sont gérées différemment dans différentes plates-formes (iOS affiche moins d’informations que Android si pousse natif est désactivées sur un périphérique, l’API peut fournir davantage d’informations que l’interface utilisateur sur les statistiques de l’émetteur).
- En dehors de l’application push peut être bloqué par les clients au niveau du système d’exploitation (iOS et Android).
- En dehors de l’application pousse apparaît comme désactivé dans l’interface utilisateur de Azure Mobile Engagement s’ils ne sont pas intégrés correctement, mais il peuvent échouer en mode silencieux à partir de l’API.
- Dans App push ne fonctionne pas si l’Engagement de Mobile Azure et la portée sont intégrés dans le Kit de développement.
- Pousse le GCM et ADM ne fonctionne pas si l’Engagement de Mobile Azure et serveur spécifique sont intégrés dans le Kit de développement (Android uniquement).
- Dans l’application et d’application pousse doit être testée séparément pour déterminer si c’est un problème d’envoi ou de la portée.
- Dans App push requiert que l’application soit ouvert pour être reçu.
- Dans App push est souvent le programme d’installation pour être filtrés par une balise d’info app opt-in ou annulations.
- Si vous utilisez une catégorie personnalisée dans la portée pour afficher les notifications dans l’application, vous devez suivre le cycle de vie correct de la notification, ou bien la notification ne peut pas être effacée lorsque l’utilisateur faire disparaître.
- Si vous démarrez une campagne avec aucune date de fin et un périphérique reçoit l’application de notification mais ne pas les afficher elle encore, l’utilisateurs continueront reçoit la notification de la prochaine connexion à l’application, même si vous arrêter manuellement la campagne.
- Pour les problèmes avec l’API Push, confirmez que vous souhaitez réellement utiliser l’API de Push au lieu de l’API atteindre (puisque l’API de l’atteindre est utilisé plus souvent) et que vous ne sont pas confondre les paramètres « charge utile » et « notifiant ».
- Tester votre campagne de push avec à la fois un périphérique connecté via Wi-Fi et 3G pour éliminer la connexion réseau sous la forme d’une source potentielle de problèmes.

## <a name="push-testing"></a>Poussez le test

### <a name="issue"></a>Problème
- Push peut être envoyé à un périphérique spécifique, basé sur un ID de périphérique.

### <a name="causes"></a>Causes

- Périphériques de test sont configurés différemment pour chaque plate-forme, mais à l’origine d’un événement dans votre application sur un périphérique de test et recherche de votre ID de périphérique dans le portail doivent utiliser pour trouver votre ID de périphérique pour toutes les plates-formes.
- Test périphériques fonctionnent différemment avec IDFA et IDFV (e/s uniquement).


## <a name="push-customization"></a>Personnalisation de push

### <a name="issue"></a>Problème
- Avancés du push de contenu d’élément ne fonctionnera pas (badge, anneau, Vibreur, image, etc.).
- Liens de push ne fonctionnent pas (en dehors de l’application, dans l’application, à un site Web, à un emplacement dans l’application).
- Affichent les statistiques de transmission qu’une émission n'a pas été envoyée à autant de personnes comme prévu (trop ou pas assez).
- Push dupliqué et reçu deux fois.
- Ne peut pas enregistrer le dispositif d’essai pour Azure Mobile Engagement pousse (avec votre propre application de nomenclature de production ou de développement).

### <a name="causes"></a>Causes

- Pour créer un lien vers un emplacement spécifique dans l’application requiert les « catégories » (Android uniquement).
- Jeux de liaison complète pour rediriger les utilisateurs vers un autre emplacement après avoir cliqué sur une notification de transmission doivent être créés dans et gérés directement par votre application et la système d’exploitation de périphérique non par Mobile Engagement. (**Remarque :** de app notifications Impossible de lier directement à des endroits app avec iOS comme pour Android.)
- Serveurs d’images externes doivent être en mesure d’utiliser HTTP « GET » et « Tête » pour présentation exécute un push de travailler (Android uniquement).
- Dans votre code, vous pouvez désactiver l’agent Azure Mobile Engagement lorsque le clavier est ouvert et que votre code de réactiver l’agent Azure Mobile Engagement lorsque le clavier est fermé afin que le clavier n’affecte pas l’apparence de votre notification (e/s uniquement).
- Certains éléments ne fonctionnent pas dans des simulations de test, mais uniquement les campagnes réelles (badge, anneau, Vibreur, image, etc.).
- Aucune donnée du côté serveur n’est enregistrée lorsque vous utilisez le bouton « Tester » push. Données sont enregistrées uniquement pour les campagnes de transmission réel.
- Pour vous aider à isoler le problème, dépannage : test, simuler et une campagne réelle puisqu’ils fonctionnent de manière légèrement différente.
- La durée de que votre « application » et les campagnes « tout temps » sont planifiées pour s’exécuter affecte des numéros de livraison dans la mesure où une campagne est uniquement remise aux utilisateurs « dans l’application » lors de l’exécution de la campagne (et les utilisateurs qui disposent de leurs paramètres de périphérique définies pour recevoir des notifications insuffisante"app »).
- Les différences entre comment gérer les Android et iOS de notifications d’application rend difficile de comparer directement les statistiques de transmission entre la version Android et iOS de votre application. Android fournit les informations de notification au niveau du système d’exploitation plus qu’iOS. Rapports Android lorsqu’une notification native est reçue, un clic sur ou supprimée dans le centre de la notification, mais iOS ne signale pas cette information, sauf si l’utilisateur clique sur la notification. 
- La principale raison qui les numéros « poussés » sont différentes de celles de l’autre que « livré » des numéros pour atteint des campagnes est que « app » et « de l’application » les notifications sont comptabilisées différemment. « Dans l’application » notifications sont gérées par l’Engagement de Mobile, mais insuffisante"app » notifications sont gérées par le centre de notification dans le système d’exploitation de votre périphérique.

## <a name="push-targeting"></a>Poussez le ciblage

### <a name="issue"></a>Problème
- Intégrée de ciblage ne fonctionne pas comme prévu.
- Ciblage de balise App Info ne fonctionne pas comme prévu.
- Ciblage de géolocalisation ne fonctionne pas comme prévu.
- Options de langue ne fonctionnent pas comme prévu.

### <a name="causes"></a>Causes

- Assurez-vous que vous avez téléchargé les balises d’info d’application via l’API ou l’Azure Mobile Engagement UI.
- La limitation de la vitesse de transmission ou quota d’émission au niveau de l’application, ou pour limiter l’audience au niveau de la campagne peut empêcher une personne de recevoir une commande spécifique, même si elles répondent à vos critères de ciblage. 
- Définition d’un « langage » diffère de celle de ciblage en fonction du pays ou de paramètres régionaux, qui sont également différent de celui de ciblage en fonction de géolocalisation basé sur un emplacement du téléphone ou GPS.
- Le message dans la langue « par défaut » est envoyé à tous les clients ne disposant pas leur périphérique parmi les autres langues, que vous spécifiez la valeur.


## <a name="push-scheduling"></a>Poussez la planification

### <a name="issue"></a>Problème
- Planification de la transmission ne fonctionne comme prévu (envoyé trop anticipée ou différée).

### <a name="causes"></a>Causes

- Les fuseaux horaires peuvent problèmes grâce à la planification, en particulier lorsque vous utilisez le fuseau horaire de l’utilisateur final.
- Les fonctionnalités avancées d’envoi peuvent retarder push.
- Ciblage en fonction de paramètres (au lieu de balises de Info App) peuvent différer de téléphone pousse car Azure Mobile Engagement peuvent avoir demander des données à partir du téléphone en temps réel avant d’envoyer une commande.
- Campagnes marketing créées sans date de fin stocke localement de la diffusion sur le périphérique et l’affiche la prochaine fois que l’application est ouverte, même si la campagne est terminée manuellement.
- Démarrage de plusieurs campagnes en même temps peut prendre de plus de temps pour analyser votre base d’utilisateurs (essayez uniquement démarrer une campagne à la fois avec un maximum de quatre, également cible uniquement aux utilisateurs actifs afin que les anciens utilisateurs ne doivent pas être analysés).
- Si vous utilisez l’option « Ignorer public, push sera envoyé aux utilisateurs via l’API » dans la section « Campagne » d’une campagne de portée, la campagne n’enverra pas automatiquement, vous devrez envoyer manuellement via l’API d’atteindre.
- Si vous utilisez une catégorie personnalisée dans la portée pour afficher les notifications dans l’application, vous devez suivre le cycle de vie correct d’une notification, sinon la notification ne peut pas être effacée lorsque l’utilisateur faire disparaître.

 
