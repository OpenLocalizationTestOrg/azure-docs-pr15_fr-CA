<properties
    pageTitle="Méthodes conseillées RemoteApp Azure | Microsoft Azure"
    description="Méthodes conseillées pour la configuration et l’utilisation d’Azure RemoteApp."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />

# <a name="best-practices-for-configuring-and-using-azure-remoteapp"></a>Méthodes conseillées pour la configuration et l’utilisation d’Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

Les informations suivantes peuvent vous aider à configurer et à utiliser efficacement les Azure RemoteApp.

## <a name="connectivity"></a>Connectivité


- Toujours utiliser la dernière version du client. À l’aide de clients plus anciens peut entraîner des problèmes de connectivité et d’autres expériences de dégradé. Activation des mises à jour automatiques des applications pour votre périphérique veillera à ce que la dernière version du client est toujours installé.
- Toujours utiliser la connexion internet la plus stable et fiable à votre disposition.  
- Utilisation uniquement prise en charge que les connexions de proxy pour des performances optimales de connectivité.  Le proxy SOCKS n’est pas pris en charge.

## <a name="applications"></a>Applications


- Enregistrez et fermez les applications RemoteApp lorsque vous avez terminé avec l’application. Ne pas fermer l’application peut entraîner la perte de données.
- Valider des applications personnalisées avant de les utiliser dans Azure RemoteApp. Cela implique de vérifier qu’ils fonctionnent sur une plate-forme de session multiples et n’utilisent pas inutile des ressources comme la mémoire et du processeur qui peut priver un autre utilisateur dans la même collection. Pour plus d’informations, téléchargez et consultez les [Application compatibilité conseillées pour les Services Bureau à distance](http://www.dabcc.com/resources/Application%20Compatibility%20Best%20Practices%20for%20Remote%20Desktop%20Services.pdf).

## <a name="configuration-and-management"></a>Configuration et gestion


- Les images de votre modèle à jour, l’installation des mises à jour logicielles et autres correctifs critiques en fonction des besoins. Cela garantit que RemoteApp d’Azure automatique mise à l’échelle afin de répondre à votre capacité, chaque instance est corrigé.  
- Assurez-vous que votre déploiement d’Active Directory Federation Services (ADFS) est sécurisés et fiables. Dans le cas contraire les authentifications de client risquent d’échouer, empêchant les utilisateurs d’accéder à Azure RemoteApp.
- Configurer des images du modèle avec les applications installées, des rôles ou des fonctionnalités telles qu’elles sont sans état. Ils ne doivent pas compter sur toutes les instances des machines virtuelles dans un service de RemoteApp est dans un état permanent.
    - Stocker toutes les données utilisateur dans les profils d’utilisateurs ou d’autres emplacements de stockage externes au service, telles que les partages ou OneDrive de fichiers locaux.
    - Banque de données partagées dans les emplacements de stockage externes au service, telles que les partages ou OneDrive de fichiers locaux.
    - Configurer les paramètres à l’échelle du système dans l’image du modèle plutôt que sur les machines virtuelles dans un service.
    - Désactiver les mises à jour logicielles automatiques pour les applications publiées - au lieu de cela les appliquer manuellement à l’image du modèle et de les tester avant de le déployer à partir du modèle.
