<properties 
   pageTitle="Notes de mise à StorSimple 8000 Series mise à jour 2 | Microsoft Azure"
   description="Décrit les nouvelles fonctionnalités, les problèmes et les solutions de contournement pour la série de mise à jour 2 StorSimple 8000."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
 <tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/24/2016"
   ms.author="v-sharos" />

# <a name="storsimple-8000-series-update-2-release-notes"></a>Notes de mise à StorSimple 8000 Series mise à jour 2  

## <a name="overview"></a>Vue d’ensemble

Les notes suivantes décrivent les nouvelles fonctionnalités et identifient les problèmes critiques pour la série de mise à jour 2 StorSimple 8000. Ils contiennent également une liste des StorSimple logiciels, pilotes et mises à jour du microprogramme de disque inclus dans cette version. 

Mise à jour 2 peut être appliqué à n’importe quel périphérique StorSimple exécutant la version (GA) ou la mise à jour de 0,1 via la mise à jour 1.2. La version de périphérique associée à mise à jour 2 est 6.3.9600.17673.

Veuillez consulter les informations contenues dans les notes de version avant de déployer la mise à jour de votre solution de StorSimple.

>[AZURE.IMPORTANT]
> 
- Il faut environ 4 à 7 heures pour installer cette mise à jour (y compris les mises à jour de Windows). 
- Mise à jour 2 a des logiciels et mises à jour du firmware SSD pilote LSI.
- De nouvelles versions, vous ne voyiez pas les mises à jour immédiatement, étant donné que nous avons un déploiement échelonné de mises à jour. Patientez quelques jours et puis d’analyse des mises à jour à nouveau en tant qu’elles ne sera bientôt plus disponible.


## <a name="whats-new-in-update-2"></a>Quelles sont les nouveautés dans la mise à jour 2

Mise à jour 2 présente les nouvelles fonctionnalités suivantes.

- **Localement épinglé volumes** – dans les versions précédentes de la série 8000 de StorSimple, les blocs de données ont été monté en cascade dans le nuage en fonction de l’utilisation. Il n’a aucun moyen de garantir que les blocs restera sur l’ordinateur local. Mise à jour 2, lorsque vous créez un volume, vous pouvez désigner un volume comme données localement épinglées et principales de ce volume ne seront pas en cascade vers le nuage. Afin que le nuage peut être utilisé pour des fins de récupération après incident et de mobilité des données des captures instantanées de volumes ajoutés localement sont toujours copiées vers le nuage pour la sauvegarde. En outre, vous pouvez modifier le type de volume (c'est-à-dire, convertir hiérarchisé volumes aux volumes ajoutés localement et volumes convertir localement épinglé à plusieurs niveaux). 

- **Améliorations de périphérique virtuel StorSimple** – précédemment, la série 8000 de StorSimple positionné le périphérique virtuel comme une solution de récupération ou de développement/test de reprise après sinistre. Il y n'a qu’un seul modèle de périphérique virtuel (modèle 1100). Mise à jour 2 présente les deux modèles de périphérique virtuel : 

     - 8010 (anciennement appelé la 1100) – aucune modification ; a une capacité de 30 To et utilise le stockage Azure standard.
     - 8020 – a une capacité de 64 To et utilise le stockage de la prime d’Azure pour améliorer les performances.

    Il y a un disque dur virtuel unique pour les deux modèles de périphérique virtuel (8010/8020). Lorsque vous démarrez le périphérique virtuel pour la première fois, il détecte les paramètres de la plate-forme et s’applique à la version du modèle approprié.

- **Améliorations de mise en réseau** – mise à jour 2 contient les améliorations de mise en réseau suivantes :

     - Plusieurs cartes réseau peut être activées pour le nuage afin que le basculement peut se produire en cas de défaillance d’une carte réseau.
     - Améliorations du routage, avec une métrique fixe pour le cloud activé des blocs.
     - Recommencer en ligne de ressources ayant échouées avant un basculement sur incident.
     - Les nouvelles alertes pour les défaillances de service.

- **Améliorations de la mise à jour** – mise à jour 1.2 et versions antérieures, la série 8000 de StorSimple a été mis à jour via deux canaux : mise à jour de Windows pour le clustering, iSCSI et ainsi de suite et Microsoft Update pour les fichiers binaires et du firmware.
    Mise à jour 2 utilise Microsoft Update pour tous les packages de mises à jour. Ceci devrait conduire à moins de temps à correction ou d’effectuer des basculements sur incident. 

- **Mises à jour du firmware** – suivantes figurent des mises à jour du firmware :
    - LSI : lsi_sas2.sys Version du produit 2.00.72.10
    - SSD uniquement (aucune mise à jour du disque dur) : XMGG, XGEG, KZ50, F6C2 et VR08

- **Prise en charge proactive** – 2 de la mise à jour permet à Microsoft d’extraire des informations de diagnostic supplémentaires à partir du périphérique. Notre équipe des opérations identifie les périphériques qui posent problème, nous sont mieux équipés pour collecter des informations à partir du périphérique et de diagnostiquer les problèmes. **En acceptant les 2 mise à jour, vous nous autorisez à cette prise en charge proactive**.    
 

## <a name="issues-fixed-in-update-2"></a>Problèmes résolus dans la mise à jour 2

Le tableau suivant fournit un récapitulatif des problèmes qui ont été résolus dans les mises à jour 2.    

| N° | Fonctionnalité | Problème | S’applique à un périphérique physique | S’applique à un périphérique virtuel |
|-----|---------|-------|--------------------------------|--------------------------------|
| 1 | Interfaces réseau | Après une mise à niveau 1 de mise à jour, le service Gestionnaire de StorSimple, a signalé que les ports Data2 et Data3 a échoué sur un contrôleur. Ce problème a été résolu. | Oui | N° |
| 2 | Mises à jour | Après une mise à niveau 1 de mise à jour, alertes d’alarme sonore s’est produite dans le portail Azure classique sur plusieurs périphériques. Ce problème a été résolu. | Oui | N° |
| 3 | Authentification d’Openstack | Lors de l’utilisation de Openstack en tant que votre fournisseur de services de cloud, vous pouvez recevoir une erreur que votre chaîne d’authentification de nuage était trop long. Ce problème a été corrigé. | Oui | N° |


## <a name="known-issues-in-update-2"></a>Problèmes connus dans la mise à jour 2

Le tableau suivant fournit un résumé des problèmes connus dans cette version.

| N° | Fonctionnalité | Problème | Commentaires / solution | S’applique à un périphérique physique | S’applique à un périphérique virtuel |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | Quorum de disque | Dans de rares cas, si la majorité des disques dans le boîtier de la EBOD d’un dispositif 8600 est déconnectée entraînant aucun quorum de disque, puis le pool de stockage est hors ligne. Il restera en mode hors connexion, même si les disques sont reconnectés. | Vous devez redémarrer le périphérique. Si le problème persiste, veuillez contacter le Support Microsoft pour les étapes suivantes. | Oui | N° |
| 2 | ID de contrôleur incorrecte | Lors d’un remplacement de contrôleur, contrôleur 0 apparaissent comme contrôleur 1. Pendant le remplacement de contrôleur, lorsque l’image est chargée à partir du nœud homologue, l’ID du contrôleur peut afficheront initialement en tant qu’ID. du contrôleur de l’homologue Dans de rares cas, ce comportement peut également survenir après un redémarrage du système. | Aucune action utilisateur n’est requise. Cette situation se résoudra de lui-même après que le remplacement du contrôleur est terminé. | Oui | N° |
| 3 | Comptes de stockage | À l’aide du service de stockage pour supprimer le compte de stockage est un scénario non pris en charge. Cela entraîne une situation dans laquelle les données utilisateur ne sont pas accessibles.|  | Oui | Oui |
| 4 | Basculement entre périphériques | Plusieurs basculements d’un conteneur de volume à partir du même périphérique source vers les équipements cibles différents ne sont pas pris en charge. Basculement à partir d’un seul périphérique mort à plusieurs périphériques rend les conteneurs de volume sur le premier échec via un périphérique perdent la propriété de données. Après un tel basculement, ces conteneurs de volume apparaît ou se comportent différemment lorsque vous les affichez dans Azure portal classique. | | Oui | N° |
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
| 15 | Applets de commande PowerShell Azure et volumes ajoutés localement | Impossible de créer un volume localement ajouté via les applets de commande PowerShell d’Azure. (N’importe quel volume que vous créez via PowerShell d’Azure va être monté en cascade.) |Toujours utiliser le service Gestionnaire de StorSimple pour configurer les volumes ajoutés localement.| Oui | N° |
| 16 |Espace disponible pour les volumes ajoutés localement | Si vous supprimez un volume localement épinglé, l’espace disponible pour les nouveaux volumes de ne peut pas être mis à jour immédiatement. Le service Gestionnaire de StorSimple met à jour l’espace local environ toutes les heures.| Patientez une heure avant de tenter de créer le nouveau volume. | Oui | N° |
| 17 | Volumes ajoutés localement | Votre travail de restauration expose la sauvegarde temporaire de snapshot dans le catalogue de sauvegarde, mais uniquement pour la durée de l’opération de restauration. En outre, il expose un groupe de disque virtuel avec le préfixe **tmpCollection** sur la page de **Stratégies de sauvegarde** , mais uniquement pour la durée de l’opération de restauration. | Ce problème peut se produire si votre opération de restauration a épinglé que localement de volumes ou un mélange de localement les volumes épinglés et hiérarchisés. Si le travail de restauration inclut uniquement les volumes hiérarchisés, puis ce problème se produira pas. Aucune intervention de l’utilisateur n’est requise. | Oui | N° |
| 18 | Volumes ajoutés localement | Si vous annulez une opération de restauration et un basculement sur incident du contrôleur se produit immédiatement le travail de restauration affiche par la suite, **Échec** et non **annulé**. Si un travail de restauration échoue et un basculement sur incident du contrôleur se produit immédiatement le travail de restauration affiche par la suite, **annulée** au lieu de **l’Échec**. | Ce problème peut se produire si votre opération de restauration a épinglé que localement de volumes ou un mélange de localement les volumes épinglés et hiérarchisés. Si le travail de restauration inclut uniquement les volumes hiérarchisés, puis ce problème se produira pas. Aucune intervention de l’utilisateur n’est requise. | Oui | N° |
| 19 |Volumes ajoutés localement | Si vous annulez une opération de restauration ou si une restauration échoue puis un basculement sur incident du contrôleur, un travail de restauration supplémentaires s’affiche sur la page **tâches** . | Ce problème peut se produire si votre opération de restauration a épinglé que localement de volumes ou un mélange de localement les volumes épinglés et hiérarchisés. Si le travail de restauration inclut uniquement les volumes hiérarchisés, puis ce problème se produira pas. Aucune intervention de l’utilisateur n’est requise. | Oui | N° |
| 20 |Volumes ajoutés localement | Si vous essayez de convertir un volume monté en cascade (créé et cloné avec mise à jour 1.2 ou version antérieur) sur un volume local épinglé et votre périphérique est espace insuffisant ou il y a une panne de nuage, l’onglet « clone » peut être endommagés.| Ce problème se produit uniquement avec les volumes qui ont été créés et cloné avec PRE-logiciel mise à jour 2. Il s’agit d’un scénario rare.|
| 21 | Conversion de volume | Ne pas mettre à jour les ACRs attachés à un volume lors de la conversion d’un volume est en cours (monté en cascade à épinglé localement ou vice versa). Mise à jour de l’ACRs peut entraîner une corruption des données. | Si nécessaire, les ACRs avant la conversion du volume de mise à jour et ne faites pas d’autres mises à jour de blocage lorsque la conversion est en cours. |

## <a name="controller-and-firmware-updates-in-update-2"></a>Mises à jour de contrôleur et le microprogramme de mise à jour 2

Cette version met à jour le pilote et le micrologiciel de disque sur votre périphérique.
 
- Pour plus d’informations sur le micrologiciel LSI mise à jour, consultez l’article 3121900 de la base de connaissances Microsoft. 
- Pour plus d’informations sur le micrologiciel du disque mise à jour, consultez l’article 3121899 de la base de connaissances Microsoft.
 
## <a name="virtual-device-updates-in-update-2"></a>Mises à jour de périphérique virtuel dans la mise à jour 2

Cette mise à jour ne peut pas être appliqué au périphérique virtuel. Nouveaux périphériques virtuels seront doivent être créés. 

## <a name="next-step"></a>Étape suivante

Découvrez comment faire pour [installer la mise à jour 2](storsimple-install-update-2.md) sur le périphérique StorSimple.
