<properties 
    pageTitle="Engagement de Mobile Azure mise en route avec les meilleures pratiques"
    description="En route pour Azure Mobile Engagement et de meilleures pratiques pour l’intégration" 
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="wesmc7777"
    manager="erikre"
    editor=""/>

<tags
    ms.service="mobile-engagement"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="mobile-multiple"
    ms.workload="mobile" 
    ms.date="10/04/2016"
    ms.author="wesmc;ricksal"/>

# <a name="azure-mobile-engagement---getting-started-guide-with-best-practices"></a>Azure Engagement Mobile - Guide de démarrage avec les meilleures pratiques

## <a name="overview"></a>Vue d’ensemble

**L’écran mobile est un espace très encombré :** Dans 2013, une étude de trouver qu'un équipement mobile moyen avait 27 applications installées. Les utilisateurs généralement consacrées à 30 heures par mois sur leurs applications. La plupart de ce temps a été consacré à la mise en réseau social et le jeu (environ 20 heures). En 2014, le marché Android avait environ 1,5 million des applications aux utilisateurs de choisir à partir de. Le magasin d’Apple contenait environ 1,2 millions d’applications. Utilisation d’une application mobile augmente encore que les développeurs sont en concurrence dans ce marché en pleine expansion. 

L’utilisateur mobile moyenne installer et désinstaller des applications à haute fréquence en fonction de la modification des intérêts et des expériences dans l’application. Pour déterminer la réussite d’une application, il devient essentiel de connaître le nombre d’utilisateurs plus que simplement installer votre application. Il est important de savoir comment votre application est utile et si cette tendance d’utilisation peut changer. Les questions suivantes sont importantes :

- Sont vos utilisateurs début pour trouver votre application inintéressants ou obsolètes ? 
- Combien d’utilisateurs ont arrêté à l’aide de votre application à tout ? 
- Dans l’application achats tendez vers le haut ou vers le bas ?
- Les utilisateurs échouent terminer le flux de travail en raison de problèmes avec l’application ou le manque d’intérêt ? 
- Peut conserver votre application utiles et pertinents en poussant le contenu actualisé à votre base d’utilisateurs ? 
- Serait ce contenu fraîche soit la même pour tous les utilisateurs ou axé sur les segments d’utilisateurs basées sur le comportement de votre application ? 
 
Réponses à des questions similaires à celles-ci pourraient aider à prolonger la vie et le chiffre d’affaires à partir de votre application. Ils peuvent vous aider également définir et de conserver votre base d’utilisateurs. 

Média liée applications ont tendance à avoir certains de la rétention plus élevée entre les utilisateurs. Une raison pour Ceci est qu’ils sont constamment fraîche contenu aux utilisateurs. Adoption précoce des notifications push utile vers un segment utilisateur a tendance à avoir un fort impact sur la rétention de l’application. 

Le programme d’Azure Mobile Engagement est conçu pour vous aider à prolonger la vie et la rétention de votre application en fournissant une méthode pour collecter et analyser des informations détaillées sur l’utilisation de votre application. Il vous permettra de classer votre base en fonction du comportement de l’utilisateur et créer des campagnes ciblées pour la livraison des notifications de transmission et les messages dans l’application pour les segments d’utilisateurs identifiés. Indicateurs de performance clés (KPI) mesurent active comment vos utilisateurs sont différents aspects de votre application. Engagement de Mobile Azure fournit les méthodes que vous devez déterminer ces indicateurs de performance clés. Il vous aide à augmenter le retour sur investissement (ROI) en fournissant l’infrastructure que nécessaire pour accroître l’engagement avec votre application mobile. 

Pour obtenir le meilleur parti de Azure Mobile Engagement, vous devez élaborer un plan bien conçu d’engagement. Votre plan vous aidera à identifier les données précises que vous devrez peut-être segmenter votre base d’utilisateurs. Cela peut être fondé sur le comportement, et des expériences dans l’application. Afin que votre plan soit réussie, il est recommandé de définir clairement l’indicateur de performance clé qui vont mesurer les objectifs de votre application. Avec les indicateurs de performances clairement définis, vous pouvez facilement incorporer la logique nécessaire dans votre application pour collecter des données de granularité fine que vous utiliserez pour analyser et évaluer vos indicateurs de performance clés. Cette rubrique est un guide des meilleures pratiques pour la définition des indicateurs de performance clés que vous utiliserez avec votre plan de projet. 


## <a name="step-1-define-your-kpis-to-fit-the-bet-model"></a>Étape 1 : Définir vos indicateurs de performance clés pour adapter le modèle de résultat


Définition des indicateurs de performance clés correctement peut s’avérer difficile pour terminer. Les applications conçues pour différents secteurs industriels ont leurs propres objectifs et les caractéristiques de l’objet. Cela peut généralement confondez pas votre approche. Pour éviter ce problème, des objectifs et des indicateurs de performance clés doivent être classées en trois catégories principales : **professionnels**, **Engagement**et **techniques**. C’est ce que nous appelons le **modèle de résultat**.

Un bon plan aura généralement des objectifs et indicateurs de performance clés qui mesurent la réussite dans chacune des catégories suivantes du modèle de résultat.


#### <a name="business-kpis"></a>Indicateurs de performance clés Business

Indicateurs de performance clés Business doivent être la partie la plus facile à créer. Vous avez probablement déjà défini dans une forme lors de la planification de votre application mobile. Ces indicateurs de performance clés généralement l’aide produit de mesure et de retour sur investissement pour vous app. La liste suivante fournit quelques exemples de KPI professionnels qui peuvent vous aider lors de la définition de vos indicateurs de performances :

- Indicateurs de performance clés de supports professionnels
    - Clic sur le nombre de publicités
    - Nombre de visites de page par utilisateur
    - Nombre d’abonnements en cours
- Indicateurs de performance clés de jeux professionnels
    - Nombre d’achats de dans l’application
    - Revenu moyen par utilisateur (ARPU)
    - Temps passé par session
    - Jours lus et en cours de niveau de jeu
- Indicateurs de performance clés de commerce électronique
    - Application de jours utilisée
    - Revenu moyen par utilisateur (ARPU)
    - Montant moyen de panier lors de l’extraction
    - Catégorie de produit pour la plupart des vues et des achats
- KPI de Business bancaire et d’assurance
    - Nombre de comptes
    - Fonctions activées
    - Offre les pages visitées
    - Alertes cliqué ou activé      



#### <a name="engagement-kpis"></a>Indicateurs de performance clés engagement

Un indicateur de performance clé Engagement est un indicateur de performance pour mesurer l’engagement de vos utilisateurs. Les tendances dans ce domaine pour déterminer la rétention de votre application. Voici quelques indicateurs de performances d’exemple pour ce type d’indicateur de performance clé :

- Utilisateurs actifs au cours des 7 derniers jours
- Compte utilisateur inactif pour les 7 derniers jours
- Nombre d’utilisateurs n’avez pas utilisé l’application des 30 derniers jours  

Évidente de certains facteurs externes pouvant influencer les indicateurs dans cette zone. Par exemple, vous pouvez envisager un périphérique mobile avec un utilisateur à tout moment. Cela peut ou ne peut pas avoir la valeur true. Une application de jeu peut ont tendance à avoir une utilisation plus intensive autour des jours fériés lorsque un joueur peut jouer plus lors du travail et l’établissement.   

Bien définies dans cette catégorie doivent vous aider à mesurer la relation entre votre application et vos clients.



#### <a name="technical-kpis"></a>Indicateurs techniques

Les indicateurs de performance de cette catégorie permettent de déterminer si votre application se comporte correctement, bloque ou blocage. Ces indicateurs peuvent mesurer l’état de santé de votre application et de déterminer les problèmes d’utilisation qui peuvent empêcher les utilisateurs de l’utilisation de l’application. Les informations collectées pour cette catégorie peuvent également contenir des informations sur les performances qui seraient pertinentes à des équipes de marketing. Les données peuvent également être utiles pour le dépannage en informatique et de la prise en charge des équipes pour aider à identifier les bogues non signalés. 
 
Voici quelques exemples d’indicateurs de performance clés techniques :

- Nombre et informations sur les exceptions non gérées ou géré 
- Estampille de dernier incident
- Clique sur le bouton dernière ou la dernière page visitée
- Utilisation de la mémoire de l’application
- Cadence d’application
- Version du système d’exploitation que l’application est en cours d’exécution
- Version de l’application

Permet de définir ces indicateurs de performance clés pour améliorer les performances d’application de mesure et d’identifier les bogues potentiels. Cette indicateurs devraient contribuer à réduire le temps que nécessaire pour fournir un correctif pour vos clients. Ils peuvent également vous aider à définir un segment de l’utilisateur qui ont un problèmes particuliers. Cette segmentation d’utilisateur vous permet de créer des campagnes pour livrer des notifications concernant les correctifs disponibles et promotions potentielles pour récupérer la satisfaction du client. 


#### <a name="playbook-exercise-1-create-your-kpi-dashboard"></a>Manuel explicatif exercice 1 : Créer votre tableau de bord d’indicateur de performance clé

Lors de la définition de votre stratégie marketing, vos indicateurs de performance clés doivent présenter une vue pour chacun de vos principaux objectifs. Ils doivent être des points de données clairement définis qui vous permettra de recueillir des informations vitales pour surveiller votre application et le comportement de l’utilisateur final.

Créer un tableau de bord d’indicateur de performance clé qui contient les informations ci-dessous

1.  Que sont les indicateurs de performance clés pour l’application ?
2.  Quelles données pointant va utiliser pour représenter chaque indicateur de performance clé ?
3.  Où se trouve les données de mon application (écran, paramètres, système...) ?
4.  Puis-je lire une séquence d’Engagement pour cet indicateur de performance clé ?

Vous pouvez utiliser la feuille de calcul **Générateur d’indicateur de performance clé** dans notre [Modèle manuel de média] [ Media Playbook link] pour des exemples et des conseils.



## <a name="step-2-your-engagement-program"></a>Étape 2 : Programme de votre Engagement


Un programme grand engagement mobile doit être considéré un composant clé de votre application. Cela doit absolument inclure un programme performant de bienvenue qui s’exécute pour un utilisateur pendant les premiers jours d’utilisation de l’application. Ceci tend à avoir un effet très positif sur l’engagement et la rétention de votre application. Des études ont montré que la majorité des utilisateurs s’arrêter à l’aide d’une application les premiers jours après l’installation. Vous souhaitez atteindre atteindre ou dépasser les intérêts de conduite attentes client très tôt pendant que l’utilisateur se concentre sur votre application. Assurez-vous de que vous présentez la valeur de clé et les avantages de votre application à vos clients. 


![](./media/mobile-engagement-getting-started-best-practices/unsegmented-push-notifications.png)

Les notifications de transmission sont la meilleure approche pour des engagements au plus tôt avec les utilisateurs d’appareils mobiles. Toutefois, précaution convient lors de la segmentation des utilisateurs pour les notifications de transmission. Car une fois qu’un utilisateur semble recevoir des spams ou des notifications inintéressantes, il peut avoir une incidence grave. En quelques clics, un utilisateur peut supprimer votre application jamais à retourner. L’utilisateur doit recevoir hautement personnalisées de valeur dans l’application au lieu de spam générique.

Une fois que les utilisateurs sont engagés activement, votre programme d’engagement peut aider lecteur d’autres aspects de l’application.

Par exemple, vous pourriez configurer une campagne qui demande vos utilisateurs actifs à évaluer votre application. Puisque ce segment utilisateur est le plus actif et a le plus d’expérience avec votre application, vous vous attendez à donner le contrôle d’accès plus précis. Une fois que vous avez un classement élevé app, elle peut aider à augmenter le téléchargement organique de votre application et réduire vos coûts d’acquisition de nouveaux clients.



#### <a name="engagement-sequence"></a>Séquence d’engagement


Un programme d’Engagement global comprend des séquences d’engagement différents. Chaque séquence a pour but d’atteindre plusieurs objectifs.


###### <a name="life-push-sequence"></a>Séquence de push de vie


Les objectifs pour une séquence de commande de vie sont différentes selon le cycle de vie de l’engagement de l’utilisateur avec l’application. Un utilisateur particulier peut être nouveau, très actif ou inactif. À différentes étapes d’un cycle de vie d’engagement, les utilisateurs peuvent bénéficier à la documentation de votre contenu actualisé dans le formulaire des conseils ou des liens. 

Par exemple un nouvel utilisateur peut doivent aider l’orientation à une application ou bénéficient d’un nouvel incentive utilisateur semblable à ce qui suit la première fois qu’ils lancement l’application...

*« Heureux de vous avoir intégré ! N’oubliez pas d’ouvrir une session pour obtenir votre 1er mois gratuit ! »*


###### <a name="behavioral-push-sequence"></a>Séquence de comportement de transmission

La séquence de push comportementale a pour but d’augmenter l’utilisation basée sur le comportement de l’utilisateur collecté pour l’application.  

Par exemple, un utilisateur très actif d’une application de football fantastique peut-être bénéficier d’engagement avec les notifications suivantes...

*« Vous êtes un fan de football graves de John ! Connectez-vous à notre section NFL et gagnez un accès gratuit à la SuperBowl. »*


###### <a name="alerting-push-sequence"></a>Séquence d’envoi d’alerte

Les utilisateurs apprécieront les actualités intéressantes sur leurs centres d’intérêt. Une séquence d’alerte push améliore l’engagement par l’envoi d’alertes en fonction des centres d’intérêt, un utilisateur a clairement montré. Cela peut être explicite lorsqu’un utilisateur sélectionne ses propres intérêts dans l’application. Il peut également être déterminé implicitement basés sur des données collectées au cours de l’interaction de l’utilisateur avec l’application.

Par exemple, l’utilisateur d’une application de Commerce électronique peut acheter régulièrement d’une marque spécifique de café que vous avez capturé avec un indicateur de performance clé de l’entreprise. L’alerte suivante peut améliorer l’engagement de cet utilisateur avec l’application.
 
*« Bonjour Wes, une de vos marques préférées de café sera en vente 25 % de réduction de la première semaine de septembre 2015. Nous vous remercions d’avoir en tant que client et assurer vous saviez. »*

###### <a name="rentention-push-sequence"></a>Séquence de push de fixation

Cette séquence vise à fidéliser les utilisateurs à l’aide un campagnes de notification push répétitives pour aider le lecteur à une habitude régulière de s’engager avec l’application. Cela peut aider à augmenter la rétention d’application si l’utilisateur tire les interactions. 

Par exemple, l’utilisateur d’une application de sport associés peut-être recevoir les notifications suivantes une fois par semaine selon les équipes favorites de l’utilisateur :

*« Pour une chance de gagner des points 200, aller vote si les Yankees de New York va gagner leur jeu cette semaine contre Toronto bleu Jays ! ».*


#### <a name="the-3w-approach"></a>L’approche de 3 w

Maîtrise les séquences de différents émetteurs vous aidera à engager avec les utilisateurs finaux. Toutefois, vous devez toujours utiliser l’approche 3W pour personnaliser vos notifications. L’approche 3W doit répondre qui, quoi et quand pour chaque notification. Si vous clairement répond à ces questions que vous notifications devez être correctement adaptée engagement.

![](./media/mobile-engagement-getting-started-best-practices/who-what-when.png)



###### <a name="who-the-user-segment-that-will-receive-messages"></a>Qui : L’utilisateur de segment qui recevra des messages

Émission des notifications à vos utilisateurs à considérer comme un canal de communication très sensibles. Assurez-vous que les notifications que vous visent à envoyer à un segment de l’utilisateur sont également une portée limitées à l’intérêt de ce segment de l’utilisateur. Une notification mal routée est très susceptible d’avoir un effet négatif sur un utilisateur. Ils peuvent le jugent spam menant à votre application en cours de désinstallation. 

Utilisez une combinaison de critères de règles de comportement et de techniques spécifiques lors de la définition des segments d’utilisateurs qui recevront les notifications. Un exemple simple qui définit un segment d’utilisateurs peut être similaire à l’instruction suivante :

« Tous les utilisateurs ayant lancement l’une application mobile pour la première fois il y a 3 jours et ont visité, la page de connexion deux fois sans réellement effectuer une connexion d’accès ».
 
Cette instruction permet d’identifier les données que vous devrez collecter pour prendre en charge un scénario spécifique.


###### <a name="what-the-message-that-you-will-send"></a>Quoi : Le message à envoyer

**Tonalité**

Dans le cadre de vos contrats, utilisez un ton qui est approprié pour votre pour vos utilisateurs segmentés. Il s’agit sans aucun doute un bon moyen de vous connecter avec vos utilisateurs finaux et de promouvoir les intérêts d’un utilisateur dans votre application. 

**Redirection**

Une notification de transmission peut être utilisée pour l’ouverture de plus de l’application. Si le message de notification fournit un contexte de diffusion d’actualités ou une promotion sur les produits, cette notification peut lien profond directement vers le bon contenu dans l’application. Pour ce faire, vous devez créer un modèle d’URL pour laisser l’application gérer la redirection. Lorsque vous travaillez sur des séquences de votre engagement, il s’agit d’une étape importante qui ne doit pas être oubliée.

La redirection peut également être gérée pour d’autres systèmes. Par exemple, avec une URL de l’Action, il est possible de rediriger les utilisateurs finaux à de nombreux autres systèmes, y compris les éléments suivants :

- Un site Web
- Une boîte aux lettres de messagerie déjà configurés
- Une boîte de SMS
- Un service d’accès à distance
- Directement dans la banque de l’application d’évaluation de l’application. 

Cela fournit de nombreuses opportunités à engager les utilisateurs finaux et à créer des règles automatiques pour améliorer les performances.


**Format de contenu**

Différents types et formats de notification de transmission :

1. **Annonces** : vous permet d’envoyer des messages publicitaires aux utilisateurs à des moments différents (en dehors de l’application, dans l’application ou à tout moment).
2. **Sondages** : activé vous permet de recueillir des informations sur les utilisateurs finaux en posant des questions. Ces réponses sont ensuite disponibles lors de la création de critères pour les utilisateurs finaux de la cible.
3. **Exécute un push de données** : vous permet d’envoyer un fichier de données binaires en base64 ou de mettre à jour l’application. Les informations contenues dans une transmission de données sont envoyées à votre application de personnaliser l’expérience des utilisateurs dans votre application. Votre application doit être conçue pour prendre en charge les données dans une transmission de données.
4. **Mosaïques (Windows Phone uniquement)** : activé vous permet d’utiliser le Service de Notification de Push de Microsoft (MPNS) pour envoyer la Notification de transmission natif contenant des données XML (pris en charge depuis le Kit de développement logiciel version 0.9.0. La charge utile finale pour les mosaïques ne doit pas dépasser 32 kilo-octets.). Le message s’affiche directement sur le carreau de votre mère.
5. **Affichage en mode Web** : affichage web est un contenu web contenant un pop-up. Ce menu contextuel apparaît lorsque l’utilisateur final a cliqué sur la notification de transmission. Un affichage web vous permet d’avoir plus d’interaction avec l’utilisateur final.
 
>[AZURE.NOTE] Assurez-vous que le contenu que vous envoyez sous forme de notifications de type Pousser est conforme à la plate-forme correspondante (iOS, Android, Windows) les lignes directrices pour le développement d’applications et l’envoi de notifications de type Pousser.

 


###### <a name="when-the-timing-of-your-campaign"></a>Quand : Le minutage de votre campagne

Lorsque est le meilleur moment pour activer une campagne déclenchement de notifications de type Pousser ? Doit être manuelle ou automatique ? Doit être récurrente ? Déterminer le moment ou la fréquence est indispensable pour stimuler les utilisateurs avec les meilleurs résultats. Pour chaque séquence de l’engagement et le scénario, vous devez spécifier lorsque sera le meilleur moment pour envoyer des notifications de type Pousser. Voici quelques exemples possibles :

![](./media/mobile-engagement-getting-started-best-practices/campaign-timing-examples.png)

Si vous envoyez des notifications de tous les jours, vous devez effectuer une réflexion que vos utilisateurs penser que vos communications comme étant du spam. 

Engagement de Mobile Azure fournit deux façons d’éviter de vos communications perçues comme étant du spam. Tout d’abord, utilisez segmentation de grain fin afin de que vous ne ciblez pas les mêmes utilisateurs. En outre, Azure Mobile Engagement fournit une fonctionnalité de « quota ». Cette fonctionnalité peut limiter les notifications envoyées pour une campagne. Par exemple, la définition d’un quota par défaut à 5 par semaine vous assurer qu’un utilisateur inclus comme partie du segment utilisateur campagne reçoit des notifications de pas plus de 5 pour la semaine.





#### <a name="playbook-exercise-2-create-your-engagement-program"></a>Manuel explicatif exercice 2 : Créer votre programme d’engagement

Prenez le temps résumer vos objectifs et définir les campagnes que vous vous attendez à lire à l’aide de séquences spécifiques. Assurez-vous que vous appliquez l’approche de 3 w pour les notifications de vos campagnes. 

Utilisez la feuille de calcul du **Programme de l’Engagement** dans le [Modèle de média manuel] [ Media Playbook link] pour des exemples et des conseils.


## <a name="step-3-app-integration"></a>Étape 3 : Intégration de l’application


#### <a name="create-a-tag-plan"></a>Créer un plan de la balise

Pour intégrer Azure Mobile Engagement dans votre application, que vous devez créer un plan de la balise. Le plan de la balise est la pierre angulaire du projet. Il définit la relation entre les spécifications de marketing, du flux de travail de l’application et les données de balise réelles collectées dans l’application pour mesurer des indicateurs de performance clés. Il indique quel analytique vous pourrez voir dans le portail. Il vous permet également de définir les segments d’utilisateurs et envoyer des notifications push ciblées à vos utilisateurs finaux. Une fois que vous disposez du plan de balise défini, ajouter le code pour l’intégrer à votre application est simple à l’aide de l’Engagement de Mobile Azure SDK.

Un plan de la balise ne doit pas baliser tous les éléments dans une application. Il doit contenir des données de balise qui fait partie de votre stratégie d’engagement mobile. Ce sera probablement différents entre les applications. Le [Modèle de média manuel] [ Media Playbook link] fourni par permet d’Azure Mobile Engagement, vous établissez un plan de balise avec une méthode donnée. Utilisez la feuille de calcul de **Plan de la balise** comme un guide pour la création de votre plan de la balise.

Lors de la définition d’une section de la balise dans la feuille de calcul, soyez très précis. Ceci est très important éviter toute confusion. Détail de chaque scénario attendu dans chaque balise à laquelle est envoyé. Inclure le nom de l’activité, où chaque balise est incorporé. Il doit être inclus dans la partie **Informative** de la feuille de calcul. La feuille de calcul de plan de balise doit être la principale référence pour le test de vérification. 

Dans la section des **données à collecter** , votre équipe de développement doit rechercher les types, les noms, les valeurs et les paires extra-info clé/valeur requises pour chaque balise qui est incorporé dans l’application.

Nous vous recommandons de consulter le plan de la balise avec toutes les équipes associées au projet. Effectuez les corrections nécessaires et confirmer que tout est clair pour les équipes de développement et de marketing.

La feuille de **cahier des charges** peut être utilisée pour aider que toutes les personnes impliquées dans le projet.


#### <a name="data-types"></a>Types de données

Il s’agit des types courants de prise en charge de données Azure Mobile engagement.

###### <a name="devices-and-users"></a>Les utilisateurs et les périphériques

Engagement de Mobile Azure identifie les utilisateurs en générant un identificateur unique pour chaque périphérique. Cet identificateur est appelé identificateur de périphérique (ou ID de périphérique). Il est généré de manière à ce que toutes les applications en cours d’exécution sur ce même périphérique partagent le même identificateur de périphérique.

###### <a name="sessions-and-activities"></a>Des activités et sessions

Une session est une instance de l’application en cours d’exécution par un utilisateur. La session s’étend entre le moment où que l’utilisateur démarre l’application, jusqu'à son arrêt.

Une activité est un regroupement logique d’un ensemble d’actions que l’application peut effectuer lors d’une session. Il s’agit généralement d’un écran particulier dans l’application, mais il peut être que n’importe quoi défini par la logique de l’application. Au minimum, vous devez baliser chaque écran ou une activité pour votre application. Cela vous permettra de comprendre le chemin d’accès utilisateur.


###### <a name="events"></a>Événements

Les événements sont utilisés pour signaler l’interaction utilisateur avec l’application. Ils peuvent être des actions instantanées, telles que le partage de contenu ou le lancement d’une vidéo. Marquage des événements vous fournira des collections de données qui indiquent la manière dont les utilisateurs interagissent avec l’application. 

###### <a name="jobs"></a>Travaux

Les travaux sont utilisés pour signaler des actions qui ont une durée. Quelques exemples :

- Exécution d’appels de l’API
- Durée d’affichage de publicités
- Durée des tâches en arrière-plan 
- Durée de traitement d’achat
- Affichage d’une vidéo


###### <a name="errors"></a>Erreurs

Les erreurs sont utilisés pour signaler des problèmes détectés par l’application. Par exemple, les actions utilisateur incorrect ou échecs des appels API.

###### <a name="application-information"></a>Informations sur l’application

Informations sur l’application (App-Info) sont utilisées pour baliser les données relatives à l’expérience utilisateur avec une application. Il est généré par une interaction utilisateur avec l’application. 

Pour une clé donnée app-info, Azure Mobile Engagement seulement effectue le suivi de la valeur la plus récente (aucun historique). App-info révèle l’état de votre application ou vos utilisateurs finaux. Par exemple l’état d’ouverture de session, ou groupe de produits préférés d’un utilisateur.

###### <a name="crash-data"></a>Données de panne

Bloquer les données collectées automatiquement par les défaillances de l’application Mobile Engagement SDK rapports non gérées par l’application. Par exemple, une exception non gérée qui se produit.


###### <a name="extra-data"></a>Données supplémentaires

Événements, des erreurs, des activités et des tâches peuvent être améliorées avec des paramètres. Il s’agit d’un développeur peut fournir des données spécifiques de l’application d’informations de fichier extra. Ceci est important pour la définition de segmentation précise. 

Par exemple, la valeur d’une balise « article » vous permettra aux utilisateurs finaux le segment en fonction de qui a visionné l’article particulier. Toutefois, qui peuvent ne pas suffire. Il peut être préférable si cette même balise « article » inclus également extra-info, par exemple « news_category » dans une activité. Il serait utile de déterminer de manière dynamique les catégories favorites de l’utilisateur. 

Extra-info est signalée comme une paire clé/valeur. Dans l’exemple pour cette application media, extra-info pour « news_category » est la valeur de cette catégorie. Par exemple, « sports », « économie » ou « politique ».





#### <a name="tag-and-sdk-integration"></a>Intégration de balise et le Kit de développement logiciel 

Pour obtenir des instructions étape par étape pour l’intégration de l’Engagement de Mobile Azure SDK dans votre application, suivez la documentation sur [l’Intégration de kit de développement logiciel d’Engagement](mobile-engagement-windows-store-integrate-engagement.md) sur le site Web Azure. Choisissez votre plate-forme cible les liens en haut de la page.

Nous vous recommandons de créer des projets pour les deux applications construites sur Azure Mobile Engagement. Une pour le développement et de test intermédiaire et l’autre pour la mise en œuvre de production. Votre équipe informatique peut promouvoir puis du reclassement de test à la production, lorsque le test d’acceptation utilisateur est réussi.



#### <a name="user-acceptance-testing-uat"></a>D’acceptation utilisateur (UAT) de test

Test d’acceptation utilisateur (UAT) implique de vérifier que tout fonctionne comme prévu. Flux de travail peuvent être effectuées et rassembler toutes les données requises en fonction de votre plan de la balise :
 
- Étiquetage des informations à mettre en place en fonction de concepts AZME documentées
- Toutes les informations dont vous avez besoin sont collectées (y compris la valeur d’info Extra, valeur d’info App)
- Nomenclature des correspondances en fonction de votre Plan de la balise
- Il n’y a aucune balise en double envoyé

Tester tous les types de comportement de notification que vous avez incorporé dans votre application

- Données des annonces, des sondages, exécute un push de l’application et dans l’application
- Affichages de texte/Web
- Mise à jour de badge, catégories



#### <a name="setup"></a>Programme d’installation

Engagement de Mobile Azure est très simple. Toute la documentation liée à l’interface utilisateur est disponible sur le site Web de l’Engagement de Mobile d’Azure, [comment naviguer dans l’interface utilisateur](mobile-engagement-user-interface-home.md).

Il est recommandé de commencer par configurer les rôles de droite et les appartenances aux rôles pour les utilisateurs de votre projet. Cela vous permet de gérer l’accès approprié à la plate-forme pour tous les utilisateurs. Vos rôles peuvent inclure :

- Administrateurs
- Développeurs
- Afficheurs 

Par la suite :
- Enregistrer votre deviceID à tester sur votre périphérique.
- Accédez aux paramètres de votre compte et définir le fuseau horaire pour que les graphiques et l’heure de livraison de notification définie pour votre fuseau horaire.
- Accédez aux paramètres de votre application et enregistrer le « application-Infos » vous avez besoin pour l’utilisateur final de cible à portée de main.

Pour plus d’informations sur l’exécution de votre première campagne de notification de transmission, consultez [comment commencer à utiliser et gérer des campagnes d’accéder à vos utilisateurs finaux](mobile-engagement-how-tos.md).



## <a name="conclusion"></a>Conclusion


Programmes d’engagement sont itératives et vous devez améliorer en permanence vous appartient lorsque vous le testez ce qui fonctionne le mieux pour votre application. 

Au départ, lors du développement d’expérience avec engagement stratégies n’essayez pas de créer une stratégie tout engagement global. Adoptez une approche étape par étape qui identifie vos indicateurs de performance clés et comment les exploiter. Stratégie d’engagement sera unique pour chaque application.

Après avoir développé une expérience vous songez à ajouter ce qui suit à vos programmes d’engagement :

- Suivi : Acquisition d’utilisateurs et que vous définissez probablement des sources de collecte de données. Engagement de mobile Azure peut être lié à des sources de collecte de données. Il vous permet de surveiller les performances de chaque source. Ces informations seront intéressantes pour optimiser votre investissement d’acquisition. 

- A / B test : c’est une partie essentielle du programme de l’Engagement. Chaque application a ses propres spécificités. A / B de test, vous pouvez améliorer votre programme d’engagement.

- GÉOLOCALISATION : Il s’agit d’une opportunité importante pour les marques. Grâce à cette fonctionnalité, vous pouvez atteindre au bon endroit et à la fois. Nous vous recommandons de vérification que vous avez rassemblé suffisamment données de comportement de l’utilisateur final avant de commencer à utiliser la géolocalisation.

- Transmission de données : envoi de données est un push invisible. Transmission de données permet la personnalisation de votre application basée sur le comportement de l’utilisateur final. Par exemple, si un segment utilisateur consulte souvent des produits de haute technologie, le propriétaire de l’application peut envoyer une transmission de données qui personnalisent sa page d’accueil avec un contenu de haute technologie.



## <a name="next-steps"></a>Étapes suivantes

- [Création d’un compte Azure Mobile Engagement](mobile-engagement-create.md).
- Visitez le site de [définir votre stratégie d’Engagement de Mobile](mobile-engagement-define-your-mobile-engagement-strategy.md) pour en savoir plus sur la définition de votre stratégie d’Engagement de Mobile.



  

<!--Image references-->


<!--Link references-->
[Media Playbook link]: https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks
