<properties 
   pageTitle="Notes de version 1.2 mise à jour de StorSimple 8000 série | Microsoft Azure"
   description="Décrit les nouvelles fonctionnalités, les problèmes et les solutions de contournement pour la série de mise à jour 1.2 StorSimple 8000."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
 <tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/18/2016"
   ms.author="alkohli" />

# <a name="storsimple-8000-series-update-12-release-notes"></a>Notes de version 1.2 mise à jour de StorSimple 8000 Series  

## <a name="overview"></a>Vue d’ensemble

Les notes suivantes décrivent les nouvelles fonctionnalités et identifient les problèmes critiques pour la série de mise à jour 1.2 StorSimple 8000. Ils contiennent également une liste des logiciels, pilotes et mises à jour du microprogramme de disque inclus dans cette version StorSimple. 

Mise à jour 1.2 peut être appliqué à n’importe quel périphérique StorSimple version (GA), mise à jour de 0,1, mise à jour de 0,2 ou logiciel de mise à jour de 0,3 en cours d’exécution. Mise à jour 1.2 n’est pas disponible si votre périphérique est en cours d’exécution 1.1 de mise à jour ou de mise à jour 1. Si votre périphérique est en cours d’exécution version (GA), veuillez [contacter le support technique de Microsoft](storsimple-contact-microsoft-support.md) pour vous aider à l’installation de cette mise à jour.

Le tableau suivant répertorie les versions du logiciel de périphérique correspondant aux mises à jour 1, 1.1 et 1.2.

| Si la mise à jour en cours d’exécution... | Il s’agit de votre version de logiciel de périphérique. |
|---------------------|---------------------------------------|
| Mise à jour 1.2          | 6.3.9600.17584                        |
| Mise à jour 1.1          | 6.3.9600.17521                        |
| Mise à jour 1.0          | 6.3.9600.17491                        |

Veuillez consulter les informations contenues dans les notes de version avant de déployer la mise à jour de votre solution de StorSimple. Pour plus d’informations, consultez Comment [Installer 1.2 mise à jour de votre périphérique StorSimple](storsimple-install-update-1.md). 

>[AZURE.IMPORTANT]
> 
- Il prend environ 5 à 10 heures pour installer cette mise à jour (y compris les mises à jour de Windows). 
- Mise à jour 1.2 a logiciel, pilote LSI et mises à jour du microprogramme de disque. Pour installer, suivez les instructions dans [Installer 1.2 mise à jour de votre périphérique StorSimple](storsimple-install-update-1.md).
- De nouvelles versions, vous ne voyiez pas les mises à jour immédiatement, étant donné que nous avons un déploiement échelonné de mises à jour. Analyse des mises à jour dans quelques jours à nouveau en tant que celles-ci ne sera bientôt plus disponible.


## <a name="whats-new-in-update-12"></a>Quelles sont les nouveautés dans la mise à jour 1.2

Ces fonctionnalités ont été tout d’abord publiées avec 1 mise à jour a été mis à disposition à un ensemble limité d’utilisateurs. Avec la version 1.2 de la mise à jour, la plupart des utilisateurs StorSimple s’affichent les améliorations et les nouvelles fonctionnalités suivantes :

- **Migration à partir de 5000-7000 en série pour les périphériques série 8000** – cette version introduit une nouvelle fonctionnalité de migration qui permet la StorSimple 5000-7000 series les utilisateurs à migrer leurs données vers un matériel physique de la gamme 8000 de StorSimple ou d’une appliance virtuelle. La fonctionnalité de migration a deux propositions de valeur clés :                                                                  

    - **La continuité d’activité**, en permettant la migration de données sur les équipements de la gamme d’équipements de la 8000 gamme 5000-7000.
    - **Offres de fonctionnalité améliorée des équipements de la gamme 8000**, telles que la gestion centralisée efficace de plusieurs solutions matérielles-logicielles via le service de gestionnaire de StorSimple, améliorer la classe du matériel et mise à jour du firmware, appliances virtuelles, mobilité des données et fonctionnalités dans le plan d’évolution.

    Consultez le [guide de migration](http://www.microsoft.com/download/details.aspx?id=47322) pour plus d’informations sur la migration d’une série de 5000-7000 StorSimple à un périphérique de la gamme 8000. 

- **Disponibilité dans le portail de l’administration d’Azure** – StorSimple est désormais disponible dans le portail d’administration d’Azure. Voir comment [déployer un dispositif de StorSimple dans le portail de l’administration d’Azure](storsimple-deployment-walkthrough-gov.md).

- **Prise en charge d’autres fournisseurs de services cloud** , les autres fournisseurs de services cloud pris en charge sont Amazon S3, S3 Amazon avec des enregistrements de ressources, HP et OpenStack (version bêta).

- **Mise à jour vers la dernière API de stockage** – avec cette version, StorSimple a été mis à jour vers le dernier service de stockage Azure API. Les périphériques série 8000 de StorSimple qui exécutent des versions de logiciel préliminaire de la mise à jour 1 (version, 0,1, 0,2 et 0,3) utilisent des versions de l’API de Service de stockage Azure datant de plus de 17 juillet 2009. Comme indiqué dans [l’annonce à propos de la suppression des versions de service de stockage](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx)de mise à jour, par le 1 août 2016, ces API sera désapprouvé. Il est impératif que vous appliquez la mise à jour de série de 8000 StorSimple 1 avant le 1 août 2016. Si vous ne parvenez pas à le faire, les périphériques StorSimple cesse de fonctionner correctement.

- **Support pour la Zone redondants stockage (ZRS)** – la mise à niveau vers la dernière version de l’API de stockage, la série 8000 de StorSimple prendra en charge Zone redondants stockage (ZRS) en plus de stockage localement redondants (LRS) et de stockage redondant Geo (GRS). Reportez-vous à cet [article sur les options de redondance de stockage Azure](../storage/storage-redundancy.md) pour plus d’informations ZRS.

- **Améliorer l’expérience de déploiement et de mise à jour initiale** : dans cette version, l’installation et le processus de mise à jour ont été améliorées. L’installation par l’intermédiaire de l’Assistant d’installation est améliorée pour fournir des commentaires à l’utilisateur si la configuration réseau et les paramètres du pare-feu sont incorrects. Les cmdlets de Diagnostics supplémentaires ont été fournis pour vous aider à la résolution des problèmes de mise en réseau du périphérique. Reportez-vous à la section [Dépannage de l’article de déploiement](storsimple-troubleshoot-deployment.md) pour plus d’informations sur les applets de commande de diagnostic nouveau utilisé pour le dépannage.

## <a name="issues-fixed-in-update-12"></a>Problèmes résolus dans la mise à jour 1.2

Le tableau suivant fournit un résumé des problèmes qui ont été résolus dans les mises à jour 1, 1.1 et 1.2.    


| N° | Fonctionnalité | Problème | Résolu dans la mise à jour | S’applique à un périphérique physique | S’applique à un périphérique virtuel |
|-----|---------|-------|-----------------|---------------------------------|--------------------------------|
| 1 | Windows PowerShell pour StorSimple | Lorsqu’un utilisateur accessible à distance le dispositif de StorSimple de StorSimple à l’aide de Windows PowerShell, puis démarrer l’Assistant installation, une panne est survenue dès que les données 0 IP a été entrée. Ce bogue est maintenant résolu dans la mise à jour 1. | Mise à jour 1 | Oui | Oui |
| 2 | Usine par défaut | Dans certains cas, lorsque vous procédez à une réinitialisation de l’usine, le dispositif de StorSimple a été bloqué et affiche ce message : **réinitialisation de la fabrique est en cours (phase 8)**. Ceci s’est produit si vous avez appuyé sur CTRL + C alors que l’applet de commande était en cours. Ce bogue a été résolu.| Mise à jour 1 | Oui | N° |
| 3 | Usine par défaut | Après la réinitialisation d’une fabrique de contrôleur double défaillant, vous ont autorisé à procéder à l’enregistrement du périphérique. Cela a entraîné une configuration système non pris en charge. Dans la mise à jour 1, un message d’erreur est affiché et l’inscription est bloquée sur un périphérique qu’a une fabrique échec réinitialisé. | Mise à jour 1 | Oui | N° |
| 4 | Usine par défaut | Dans certains cas, les alertes de false incompatibilité positifs ont été générées. Alertes de non concordance incorrecte seront générés n’est plus sur les périphériques qui exécutent la mise à jour 1. | Mise à jour 1 | Oui | N° |
| 5 | Usine par défaut | Si une réinitialisation usine a été interrompue avant la fin, le périphérique est entré en mode de récupération et vous n'a pas autorisé à accéder à Windows PowerShell pour StorSimple. Ce bogue a été résolu. | Mise à jour 1 | Oui | N° |
| 6 | Reprise après sinistre | Un bogue de reprise après sinistre a été corrigé dans lequel DR échoue lors de la découverte des sauvegardes sur le périphérique cible. | Mise à jour 1 | Oui | Oui |
| 7 | Voyants de surveillance | Dans certains cas, la surveillance des voyants à l’arrière du matériel n’indique pas état correct. Le voyant bleu a été désactivé. DONNÉES de 0 et de 1 voyants de données ont été clignotant même lorsque ces interfaces n’étaient pas configurés. Le problème a été résolu et la surveillance des voyants indiquent désormais l’état correct.  | Mise à jour 1 | Oui | N° |
| 8 | Voyants de surveillance | Dans certains cas, après avoir appliqué la mise à jour 1, la lumière bleue sur le contrôleur active désactivé ce qui le rend difficile à identifier le contrôleur actif. Ce problème a été corrigé dans cette version du patch.| Mise à jour 1.2 | Oui | N° |
| 9 | Interfaces réseau | Dans les versions précédentes, un périphérique StorSimple configuré avec une passerelle non routable peut travailler hors connexion. Dans cette version, la métrique de routage pour les données de 0 a été effectuée la plus basse ; Par conséquent, même si d’autres interfaces réseau sont compatibles sur le nuage, tout le trafic de nuage à partir du périphérique est acheminé via données 0. | Mise à jour 1 | Oui | Oui | 
| 10 | Sauvegardes | Un bogue dans la mise à jour 1 qui a provoqué les sauvegardes en échec après 24 jours a été corrigé dans la version 1.1 de mise à jour de correctif. | Mise à jour 1.1 | Oui | Oui |
| 11 | Sauvegardes | Un bogue dans les versions précédentes a entraîné une baisse des performances pour les captures instantanées de nuage avec un taux de modification faibles. Ce bogue a été corrigé dans cette version du patch.| Mise à jour 1.2 | Oui | Oui |
| 12 | Mises à jour | Un bogue dans la mise à jour 1 qui a signalé un échec de la mise à niveau et qui a provoqué les contrôleurs à passer en mode de récupération, a été corrigé dans cette version du patch.| Mise à jour 1.2 | Oui | Oui |


## <a name="known-issues-in-update-12"></a>Problèmes connus dans la mise à jour 1.2

Le tableau suivant fournit un résumé des problèmes connus dans cette version.

| N° | Fonctionnalité | Problème | Commentaires/solution | S’applique à un périphérique physique | S’applique à un périphérique virtuel |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | Quorum de disque | Dans de rares cas, si la majorité des disques dans le boîtier de la EBOD d’un dispositif 8600 est déconnectée aucun quorum de disque, ce qui entraîne alors le pool de stockage sera en mode hors connexion. Il restera en mode hors connexion, même si les disques sont reconnectés. | Vous devez redémarrer le périphérique. Si le problème persiste, veuillez contacter le Support Microsoft pour les étapes suivantes. | Oui | N° |
| 2 | ID de contrôleur incorrecte | Lors d’un remplacement de contrôleur, contrôleur 0 apparaissent comme contrôleur 1. Pendant le remplacement de contrôleur, lorsque l’image est chargée à partir du nœud homologue, l’ID du contrôleur peut afficheront initialement en tant qu’ID. du contrôleur de l’homologue Dans de rares cas, ce comportement peut également survenir après un redémarrage du système. | Aucune action utilisateur n’est requise. Cette situation se résoudra de lui-même après que le remplacement du contrôleur est terminé. | Oui | N° |
| 3 | Comptes de stockage | À l’aide du service de stockage pour supprimer le compte de stockage est un scénario non pris en charge. Cela entraîne une situation dans laquelle les données utilisateur ne sont pas accessibles. | Oui | Oui |
| 4 | Basculement entre périphériques | Plusieurs basculements d’un conteneur de volume à partir du même périphérique source vers les équipements cibles différents ne sont pas pris en charge. Basculement entre des périphériques à partir d’un seul périphérique mort à plusieurs périphériques rend les conteneurs de volume sur le premier échec via un périphérique perdent la propriété de données. Après un tel basculement, ces conteneurs de volume apparaît ou se comportent différemment lorsque vous les affichez dans Azure portal classique. | | Oui | N° |
| 5 | Installation | Au cours de la carte de StorSimple pour l’installation de SharePoint, vous devez fournir une adresse IP du périphérique afin que l’installation se termine avec succès.    | | Oui | N° |
| 6 | Proxy Web | Si votre configuration du serveur proxy web a HTTPS comme protocole spécifié, puis votre dispositif-service communication est affectée et accédera l’appareil mobile en mode hors connexion. Prise en charge sera également générée dans le processus, consomme énormément de ressources sur votre périphérique. | Assurez-vous que l’URL du proxy web a HTTP comme protocole spécifié. Pour plus d’informations, accédez au [proxy web de configurer votre périphérique](storsimple-configure-web-proxy.md). | Oui | N° |
| 7 | Proxy Web | Si vous configurez et activez le proxy web sur un périphérique enregistré, vous devrez redémarrer le contrôleur actif sur le périphérique. | | Oui | N° |
| 8 | Temps de latence élevé nuage et la charge de travail d’e/s élevée | Lorsque votre périphérique StorSimple rencontre une combinaison de latences de nuage très élevé (ordre de secondes) et la charge de travail d’e/s élevée, les volumes de périphérique passer dans un état dégradé et les e/s peut échouer avec une erreur « périphérique non prêt ». | Vous devez redémarrer les contrôleurs de périphérique manuellement ou d’effectuer un basculement sur incident de périphérique pour résoudre ce problème. | Oui | N° |
| 9 | PowerShell Azure | Lorsque vous utilisez l’applet de commande Get-AzureStorSimpleStorageAccountCredential de **des StorSimple & #124 ; Select-Object - attente d’abord 1 -** pour sélectionner le premier objet de sorte que vous pouvez créer un nouvel objet **VolumeContainer** , l’applet de commande renvoie tous les objets. | Retour à la ligne l’applet de commande entre parenthèses comme suit : **(Get-Azure-StorSimpleStorageAccountCredential) & #124 ; Select-Object - attente d’abord 1 -** | Oui | Oui |
| 10| Migration | Lorsque plusieurs conteneurs de volume sont passées pour la migration, l’ATE de la dernière sauvegarde est précise uniquement pour le premier conteneur de volume. En outre, migration parallèle démarrera après avoir migrent les sauvegardes des 4 premiers dans le premier conteneur de volume. | Nous vous recommandons de migrer un conteneur de volume à la fois. | Oui | N° |
| 11| Migration | Après la restauration, les volumes ne sont pas ajoutés à la stratégie de sauvegarde ou du groupe de disque virtuel. | Vous devez ajouter ces volumes à une stratégie de sauvegarde pour créer des sauvegardes. | Oui | Oui |
| 12| Migration | Une fois la migration terminée, le périphérique série 5000/7000 ne doit pas accéder les conteneurs de données migrées. | Nous vous recommandons de supprimer les conteneurs de données ayant migré après que la migration est terminé et validé. | Oui | N° |
| 13| Clone et reprise après sinistre | Un périphérique StorSimple 1 de mise à jour en cours d’exécution ne peut pas cloner ou effectuer la reprise après sinistre à un périphérique qui exécute le logiciel de 1 avant mise à jour. | Vous devez mettre à jour le périphérique cible pour la mise à jour de 1 pour permettre ces opérations | Oui | Oui |
| 14 | Migration | Sauvegarde de la configuration de la migration peut échouer sur un périphérique de la gamme 5000-7000 lorsqu’il existe des groupes de volumes sans volume associé. | Supprimer tous les groupes de volume vide sans volume associée, puis relancez la sauvegarde de la configuration.| Oui | N° |

## <a name="physical-device-updates-in-update-12"></a>Mises à jour de périphérique physique de la mise à jour 1.2

Si la mise à jour de correctif 1.2 est appliqué à un périphérique physique (exécutant des versions antérieures à la mise à jour 1), la version du logiciel devient 6.3.9600.17584.

## <a name="controller-and-firmware-updates-in-update-12"></a>Contrôleur à jour dans la mise à jour 1.2

Cette version met à jour le pilote et le micrologiciel de disque sur votre périphérique.
 
- Pour plus d’informations sur la mise à jour du contrôleur SAS, reportez-vous à la section [1 de mise à jour pour les contrôleurs LSI SAS dans la solution matérielle-logicielle de Microsoft Azure StorSimple](https://support.microsoft.com/kb/3043005). 

- Pour plus d’informations sur le micrologiciel du disque mise à jour, consultez le [microprogramme du disque 1 de mise à jour pour la solution matérielle-logicielle de Microsoft Azure StorSimple](https://support.microsoft.com/kb/3063416).
 
## <a name="virtual-device-updates-in-update-12"></a>Mises à jour de périphérique virtuel dans la mise à jour 1.2

Cette mise à jour ne peut pas être appliqué au périphérique virtuel. Nouveaux périphériques virtuels seront doivent être créés. 

## <a name="next-steps"></a>Étapes suivantes

- [Installez la mise à jour 1.2 sur votre périphérique](storsimple-install-update-1.md).
 
