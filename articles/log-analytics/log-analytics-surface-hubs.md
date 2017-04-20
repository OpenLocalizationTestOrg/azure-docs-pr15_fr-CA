<properties
    pageTitle="Surveiller des concentrateurs de Surface avec journal Analytique | Microsoft Azure"
    description="Utiliser la solution de concentrateur de Surface pour effectuer le suivi de l’état de santé de votre Surface de concentrateurs et de comprendre comment ils sont utilisés."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="banders"/>

# <a name="monitor-surface-hubs-with-log-analytics"></a>Moniteur de concentrateurs Surface avec journal Analytique

Cet article décrit comment vous pouvez utiliser la solution de concentrateur de Surface dans journal Analytique pour surveiller les périphériques du concentrateur de Microsoft Surface avec la Suite de gestion des opérations Microsoft (OMS). Analytique de journal vous permet de suivre l’état de santé de vos concentrateurs de Surface ainsi que de comprendre comment ils sont utilisés.

Chaque concentrateur de Surface a l’Agent de surveillance de Microsoft installé. Son par le biais de l’agent que vous pouvez envoyer des données à partir de votre Surface de supervision d’OMS. Les fichiers journaux sont lus à partir de votre Surface de concentrateurs et de sont puis sont envoyés au service OMS. Des problèmes comme des serveurs est hors ligne, le calendrier ne synchronise ne pas, ou si le compte de l’appareil est impossible de se connecter dans Skype figurent dans OMS dans le tableau de bord de concentrateur de Surface. En utilisant les données dans le tableau de bord, vous pouvez identifier les périphériques qui ne sont pas en cours d’exécution, ou qui sont d’autres problèmes et éventuellement appliquer des correctifs pour les problèmes détectés.


## <a name="installing-and-configuring-the-solution"></a>L’installation et la configuration de la solution

Utilisez les informations suivantes pour installer et configurer la solution. Pour gérer vos concentrateurs de Surface à partir de la Suite de gestion des opérations (OMS) Microsoft, vous devez les éléments suivants :

- Un abonnement valide [d’OMS](http://www.microsoft.com/oms).
- Un niveau [d’abonnement de l’OMS](https://azure.microsoft.com/pricing/details/log-analytics/) qui prendra en charge le nombre de périphériques que vous souhaitez surveiller. OMS de tarification varie en fonction de combien de périphériques est inscrits et la quantité de données qu’il des processus. Vous voudrez en tenir compte lorsque vous planifiez votre déploiement de concentrateur de Surface.

Ensuite, vous va ajouter un abonnement OMS pour votre abonnement Microsoft Azure existant ou créer un nouvel espace de travail directement via le portail de l’OMS. Pour obtenir des instructions détaillées pour l’utilisation de chaque méthode est [mise en route de journal Analytique](log-analytics-get-started.md). Une fois l’abonnement OMS est défini, il existe deux façons de s’inscrire à vos périphériques de concentrateur de Surface :

- Automatiquement par le biais de InTune
- Manuellement à l’aide de **paramètres** sur le périphérique du concentrateur de Surface.

## <a name="set-up-monitoring"></a>Configurer l’analyse

Vous pouvez surveiller la santé et l’activité de votre centre de Surface à l’aide de journal Analytique de l’OMS. Vous pouvez inscrire le concentrateur de Surface dans OMS en utilisant InTune ou localement à l’aide des **paramètres** sur le concentrateur de Surface.

## <a name="connect-surface-hubs-to-oms-through-intune"></a>Connecter des concentrateurs de Surface à OMS via InTune

Vous aurez besoin de l’ID de l’espace de travail et de la clé de l’espace de travail de l’espace de travail OMS qui va gérer vos concentrateurs de Surface. Vous pouvez obtenir ceux à partir du portail de l’OMS.

InTune est un produit Microsoft qui vous permet de gérer de manière centralisée les paramètres de configuration OMS qui sont appliqués à un ou plusieurs de vos périphériques. Suivez ces étapes pour configurer vos périphériques via InTune :

1. Ouvrez une session pour InTune.
2. Accédez à **paramètres** > **Sources connectées**.
3. Créer ou modifier une stratégie basée sur le modèle de concentrateur de Surface.
4. Accédez à la section de l’OMS (Azure des perspectives opérationnelles) de la stratégie et ajoutez *l’ID de l’espace de travail* et de la *Clé de l’espace de travail* à la stratégie.
5. Enregistrer la stratégie.
6. Associez la stratégie de groupe approprié de périphériques.

  ![Stratégie de InTune](./media/log-analytics-surface-hubs/intune.png)

InTune puis de synchroniser les paramètres de l’OMS avec les périphériques dans le groupe cible, leur inscription dans votre espace de travail de l’OMS.

## <a name="connect-surface-hubs-to-oms-using-the-settings-app"></a>Connecter des concentrateurs de Surface de l’OMS, à l’aide de l’application de paramètres

Vous aurez besoin de l’ID de l’espace de travail et de la clé de l’espace de travail de l’espace de travail OMS qui va gérer vos concentrateurs de Surface. Vous pouvez obtenir ceux à partir du portail de l’OMS.

Si vous n’utilisez pas InTune pour gérer votre environnement, vous pouvez inscrire des périphériques manuellement à l’aide de **paramètres** sur chaque concentrateur de Surface :

1. À partir de votre Surface de supervision, ouvrir les **paramètres**.
2. Entrez les informations d’identification du admin de périphérique lorsque vous y êtes invité.
3. Cliquez sur **ce périphérique**et la section de **surveillance**, cliquez sur **Configurer les paramètres d’OMS**.
4. Sélectionnez **Activer l’analyse**.
6. Dans la boîte de dialogue Paramètres OMS, tapez l' **ID de l’espace de travail** et la **Clé de l’espace de travail**.  
  ![Paramètres](./media/log-analytics-surface-hubs/settings.png)
7. Cliquez sur **OK** pour terminer la configuration.

Une confirmation s’affiche, vous indiquant si la configuration de l’OMS a été appliquée sur le périphérique. S’il s’agissait, un message s’affiche indiquant que l’agent est correctement connecté au service OMS. Le périphérique démarre ensuite l’envoi des données de l’OMS, où vous pouvez afficher et agir dessus.

## <a name="monitor-surface-hubs"></a>Concentrateurs de Surface de moniteur

Surveillance de vos concentrateurs de Surface à l’aide d’OMS est très telles que la surveillance de tous les autres périphériques inscrits.

1. Ouvrez une session sur le portail de l’OMS.
2. Accédez au tableau de bord du pack solution concentrateur de Surface.
3. La santé de votre périphérique s’affiche.

  ![Tableau de bord de concentrateur de surface](./media/log-analytics-surface-hubs/surface-hub-dashboard.png)

Vous pouvez créer des [alertes](log-analytics-alerts.md) basées sur les recherches de journal existant ou personnalisé. En utilisant les données collectées par l’OMS de vos concentrateurs de Surface, vous pouvez rechercher pour les problèmes et d’alerte sur les conditions que vous définissez pour vos périphériques.


## <a name="next-steps"></a>Étapes suivantes

- [Recherche de journal de journal Analytique](log-analytics-log-searches.md) permet d’afficher les données détaillées de concentrateur de Surface.
- Créer des [alertes](log-analytics-alerts.md) pour vous avertir lorsque les problèmes se produisent avec vos concentrateurs de Surface.
