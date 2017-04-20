<properties
    pageTitle="Intégration du SDK Android Azure Engagement Mobile"
    description="Dernières mises à jour et les procédures pour Android SDK pour Azure Mobile Engagement"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="piyushjo" />

# <a name="release-notes"></a>Notes de publication

## <a name="423-08102016"></a>4.2.3 (10/08/2016)

- Aucun verrou plus de Wi-Fi.
- Résoudre un blocage lors de l’appel getDeviceId avant init (bogue introduit dans 4.2.0).

## <a name="422-05172016"></a>4.2.2 (17/05/2016)

- Améliorations de la stabilité.

## <a name="421-05102016"></a>4.2.1 (10/05/2016)

- Sécurité : désactiver l’accès de fichier local de mode web.
- Sécurité : suppression de `EngagementPreferenceActivity` classe qui étend obsolètes et non sécurisé `PreferenceActivity` classe.
- Sécurité : portée les activités sont maintenant documentées pour utiliser `exported="false"`, cet indicateur peut également être utilisé dans les versions précédentes du Kit de développement logiciel.

## <a name="420-03112016"></a>4.2.0 (11/03/2016)

- Le Kit de développement est désormais sous licence MIT.
- Permet de spécifier un identificateur de périphérique personnalisé au moment de l’initialisation du Kit de développement logiciel.

## <a name="415-02012016"></a>4.1.5 (01/02/2016)

- Améliorations de la stabilité.

## <a name="414-01262016"></a>4.1.4 (26/01/2016)

- Améliorations de la stabilité.

## <a name="413-1292015"></a>4.1.3 (12/9/2015)

- Améliorations de la stabilité.

## <a name="412-11252015"></a>4.1.2 (25/11/2015)

- Améliorations de la stabilité.

## <a name="411-11042015"></a>4.1.1 (04/11/2015)

- Améliorations de la stabilité.

## <a name="410-08252015"></a>4.1.0 (25/08/2015)

- Gérer le nouveau modèle d’autorisation pour Android M.
- Pouvez désormais configurer les fonctionnalités d’emplacement lors de l’exécution au lieu d’utiliser `AndroidManifest.xml`.
- Résoudre un bogue de l’autorisation : Si vous utilisez `ACCESS_FINE_LOCATION`, puis `ACCESS_COARSE_LOCATION` n’est plus nécessaire.
- Améliorations de la stabilité.

## <a name="400-07062015"></a>4.0.0 (06/07/2015)

-   Protocole interne modifications analytique et diffusion plus fiable.
-   Push natif (GCM/ADM) est désormais également utilisé pour dans les notifications de l’application vous devez configurer les informations d’identification de push natif pour n’importe quel type de campagne de push.
-   Corriger la notification de la vue d’ensemble : ils ont été affichés de 10 s uniquement après avoir lancé.
-   Résoudre un bogue dans l’affichage web : en cliquant sur un lien également l’exécution de l’URL de l’action par défaut.
-   Résoudre un incident rare lié à la gestion du stockage local.
-   Corriger la gestion de chaîne d’une configuration dynamique.
-   Mise à jour du CLUF.

## <a name="300-02172015"></a>3.0.0 (17/02/2015)

-   Version initiale de l’Engagement Mobile Azure
-   configuration de l’appId est remplacée par une configuration de chaîne de connexion.
-   Supprimer API pour envoyer et recevoir des messages XMPP arbitraires à partir des entités XMPP arbitraires.
-   Supprimer API pour envoyer et recevoir des messages entre les périphériques.
-   Améliorations de la sécurité.
-   Suivi de lecture de Google et de SmartAd est supprimé.
