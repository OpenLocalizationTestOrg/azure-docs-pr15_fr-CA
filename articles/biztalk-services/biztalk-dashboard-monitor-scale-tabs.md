<properties 
    pageTitle="Tableau de bord, le moniteur, l’échelle, configurer et connexions hybride dans les Services BizTalk | Microsoft Azure" 
    description="En savoir plus sur les contrôles et de surveiller les performances sur les onglets de portail classiques pour les Services BizTalk : tableau de bord, moniteur, échelle, configurer et hybride connexions. MABS, WABS" 
    services="biztalk-services" 
    documentationCenter="" 
    authors="MandiOhlinger" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="biztalk-services" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/23/2016" 
    ms.author="mandia"/>




# <a name="review-the-dashboard-monitor-scale-configure-and-hybrid-connection-tabs"></a>Passez en revue les onglets de tableau de bord, moniteur, échelle, configurer et connexion de hybride

Après avoir créé votre BizTalk Service et que vous déployez votre application, vous pouvez modifier certains paramètres du Service de BizTalk et surveiller les performances de l’application. 

Lorsque vous ouvrez le portail classique Azure, vous revenez automatiquement à l’onglet **Tous les éléments** . Pour afficher votre BizTalk Service, sélectionnez votre BizTalk Service dans l’onglet **Tous les éléments** , ou cliquez sur l’onglet **SERVICES BIZTALK** ; puis sélectionnez le nom de votre BizTalk Service.

Une nouvelle fenêtre s’ouvre avec les onglets suivants. Cette rubrique décrit ces onglets.

## <a name="quick-start-quick-startquickstart"></a>Démarrage rapide)![Démarrage rapide][QuickStart])
Selon l’édition de Services BizTalk, toutes les options répertoriées ne soient pas disponibles. 
<table border="1">
    <tr>
        <td><strong>Obtenez les outils</strong></td>
        <td>Télécharger le SDK des Services BizTalk pour installer les modèles de projet Visual Studio sur votre ordinateur de développement local. Ces modèles créent des <strong>Services BizTalk</strong> (pont) et les projets Visual Studio des <strong>Artefacts de Service BizTalk</strong> (transformation) qui sont déployés dans votre BizTalk Service.
        <br/><br/>
        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302335">Comment faire démarrer à l’aide du SDK des Services BizTalk Azure</a> et <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=241589">installer le SDK des Services BizTalk Azure</a> répertorie les étapes de la mise en route.
        </td>
    </tr>
    <tr>
        <td><strong>Créer des accords de partenariat</strong></td>
        <td>Ouvre le portail de Services Azure BizTalk hébergés sur Azure où vous ajoutez des partenaires et créez X12, AS2 et les accords de EDIFACT EDI.
        <br/><br/>
        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configuration des composants pour la messagerie EDI sur le portail de Services BizTalk</a> répertorie les étapes de la mise en route.
        </td>
    </tr>

<tr>
        <td><strong>Pour en savoir plus sur les Services BizTalk</strong></td>
        <td>Atteindre <a HREF="http://azure.microsoft.com/documentation/services/biztalk-services/">Centre de formation</a> pour en savoir plus sur les Services de BizTalk Azure.</td>
</tr>
</table>


Dans la barre des tâches en bas, vous pouvez :

<table border="1">

<tr>
<td><strong>Gérer les</strong> votre déploiement d’application</td>
<td>Ouvre le portail Azure BizTalk Services. Le portail de Services de BizTalk est l’entrée de configuration EDI, y compris l’ajout de partenaires et de création de X12, AS2 et les accords de EDIFACT.
<br/><br/>
Ceci est identique à la <strong>Création d’accords de partenariat</strong> sur l’onglet <strong>Quick Start</strong> .
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configuration des composants pour la messagerie EDI sur le portail de Services BizTalk</a> fournit plus d’informations sur le portail de Services de BizTalk.</td>
</tr>

<tr>
<td><strong>Informations de connexion</strong> du Namespace de contrôle d’accès</td>
<td>Lorsque vous sélectionnez des informations de connexion, puis le Namespace de contrôle d’accès, par défaut l’émetteur et par défaut une clé s’affichent. Vous pouvez copier ces valeurs.
<br/><br/>
Vous pouvez également ouvrir le portail de contrôle d’accès. <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">Créer un contrôle d’accès Namespace</a> fournit plus d’informations sur le portail de contrôle d’accès.</td>
</tr>

<tr>
<td><strong>Synchroniser les clés de</strong> dans le compte de stockage</td>
<td>Lorsque vous créez un compte de stockage, une clé primaire et la clé secondaire sont automatiquement créés. Ces clés de cryptage de contrôlent l’accès à votre compte de stockage. Votre BizTalk Service utilise automatiquement la clé primaire. <strong>Les clés de synchronisation</strong> permettent aux utilisateurs de basculer entre la clé primaire et la clé secondaire sans interrompre le BizTalk Service.
<br/><br/>
Par exemple, vous souhaitez que le BizTalk Service pour utiliser une nouvelle clé primaire pour le compte de stockage. Pour ce faire :
<br/><br/>
<ol>
<li>Sélectionnez votre BizTalk Service et sélectionner les <strong>Clés de la synchronisation</strong>. Sélectionnez la clé secondaire. Lorsque vous effectuez cette opération, le BizTalk Service démarre à l’aide de la clé secondaire.</li>
<li>Dans le portail Azure classique, sélectionnez votre compte de stockage et régénérer la clé primaire. N’oubliez pas que votre BizTalk Service à l’aide de la clé secondaire.</li>
<li>Sélectionnez votre BizTalk Service et sélectionner les <strong>Clés de la synchronisation</strong>. Maintenant, sélectionnez la clé primaire. Il s’agit de la nouvelle clé primaire vous régénéré.</li>
<li>Dans le portail Azure classique, sélectionnez votre compte de stockage et régénérer la clé secondaire.</li>
</ol>
<br/>
Ce processus est appelé « clés de substitution ». L’objectif est de permettre aux utilisateurs de basculer entre la clé primaire et la clé secondaire sans interrompre le BizTalk Service.</td>
</tr>

<tr>
<td><strong>Supprimer</strong> votre application</td>
<td>Lorsque vous sélectionnez Supprimer, votre BizTalk Service et déployés de tous les éléments sont supprimés.</td>
</tr>
</table>


## <a name="dashboard"></a>Tableau de bord
Selon l’édition de Services BizTalk, toutes les options répertoriées ne soient pas disponibles. 

Lorsque vous sélectionnez le nom de votre BizTalk Service, l’onglet tableau de bord s’affiche. Dans le tableau de bord, vous pouvez :

##### <a name="usage-overview-shows-the-number-of-used-hybrid-connections"></a>Vue d’ensemble de l’utilisation : Affiche le nombre de connexions utilisées de hybride
Affiche également l’utilisation de données en Go. 

##### <a name="metric-graph-shows-a-fixed-list-of-performance-metrics"></a>Métrique graphique : Affiche une liste fixe de mesures de performances
Ces métriques fournissent des valeurs en temps réel sur l’état de santé du BizTalk Service. Vous pouvez également choisir les valeurs **relatives** ou **absolues** et la plage horaire **intervalle** des mesures qui sont affichés dans le graphique. 

Pour une description de ces métriques de performances, consultez [Statistiques disponibles](#Metrics) dans cette rubrique.


##### <a name="quick-glance-lists-your-biztalk-service-properties"></a>Coup de œil rapide : Répertorie les propriétés du Service de BizTalk

<table border="1">

<tr>
<td><strong>Mettre à jour les informations d’identification de la base de données de suivi</strong></td>
<td>Modifie le nom d’utilisateur et le mot de passe utilisé pour vous connecter à la base de données de suivi.</td>
</tr>
<tr>
<td><strong>Mettre à jour le certificat SSL</strong></td>
<td>Permet d’actualiser le BizTalk Service pour utiliser un certificat SSL différent. Un certificat SSL auto-signé est créé automatiquement lorsque vous <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">créez le BizTalk Service</a>.</td>
</tr>
<tr>
<td><strong>Télécharger le certificat</strong></td>
<td>Vous pouvez télécharger le certificat SSL utilisé par votre BizTalk Service sur un ordinateur local.</td>
</tr>
<tr>
<td><strong>État</strong></td>
<td>Affiche l’état actuel de votre BizTalk Service. Consultez <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=329870">Services BizTalk : graphique d’état de Service</a>. </td>
</tr>
<tr>
<td><strong>URL du service</strong></td>
<td>L’URL de votre Service de BizTalk. C’est la même que l' <strong>URL de domaine</strong> entré lors de la création de votre BizTalk Service.</td>
</tr>
<tr>
<td><strong>Adresse IP virtuelle publique (VIP)</strong></td>
<td>L’adresse IP affectée à votre BizTalk Service. Il est utilisé pour tous les points de terminaison d’entrée et est l’adresse source pour le trafic sortant. Cette adresse IP appartient à votre Service de BizTalk, dans la mesure où il est créé. Si vous supprimez le BizTalk Service, l’adresse IP est attribuée à un autre Service de BizTalk.</td>
</tr>
<tr>
<td><strong>Namespace de l’ACS</strong></td>
<td>Authentifie avec le Service BizTalk.</td>
</tr>
<tr>
<td><strong>Édition</strong></td>
<td>Listes de l’édition entré lors de la création du BizTalk Service.</td>
</tr>
<tr>
<td><strong>Emplacement</strong></td>
<td>Affiche la région géographique qui héberge le BizTalk Service.</td>
</tr>
<tr>
<td><strong>Créé</strong></td>
<td>Affiche la date et l’heure de que création du BizTalk Service.</td>
</tr>
<tr>
<td><strong>Base de données de suivi</strong></td>
<td>Le nom de base de données de SQL Azure qui stocke les tables de suivi utilisées par votre BizTalk Service. 
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">Exigences expliqué</a> fournit des détails sur la base de données de suivi.</td>
</tr>
<tr>
<td><strong>Stockage de surveillance / d’archivage</strong></td>
<td>Le nom du compte de stockage Azure qui stocke la sortie de surveillance de votre BizTalk Service.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">Exigences expliqué</a> fournit des détails sur le compte de stockage.</td>
</tr>
<tr>
<td><strong>Nom de l’abonnement</strong></td>
<td>Répertorie l’abonnement qui héberge le BizTalk Service. L’abonnement régit l’accès au portail Azure classique.</td>
</tr>
<tr>
<td><strong>ID de l’abonnement</strong></td>
<td>Lorsqu’un abonnement est créé, un ID d’abonnement est automatiquement généré. Lorsque vous utilisez d’autres API, vous devez entrer l’ID d’abonnement.</td>
</tr>
</table>

[Les Services BizTalk : mise en service portal de classique à l’aide de Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280) répertorie les étapes pour créer un BizTalk Service.


##### <a name="manage-connection-information-sync-keys-and-delete-in-the-task-bar"></a>Gérer les informations de connexion, les clés de la synchronisation et les supprimer dans la barre des tâches :

<table border="1">

<tr>
<td><strong>Gérer les</strong> votre déploiement d’application</td>
<td>Ouvre le portail de Services Azure BizTalk. Le portail de Services de BizTalk est l’entrée de configuration EDI, y compris l’ajout de partenaires et de création de X12, AS2 et les accords de EDIFACT.
<br/><br/>
Ceci est identique à la <strong>Création d’accords de partenariat</strong> sur l’onglet <strong>Quick Start</strong> .
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configuration des composants pour la messagerie EDI sur le portail de Services BizTalk</a> fournit plus d’informations sur le portail de Services de BizTalk.</td>
</tr>
<tr>
<td><strong>Informations de connexion</strong> du Namespace de contrôle d’accès</td>
<td>Affiche le Namespace de contrôle d’accès, par défaut l’émetteur et les valeurs par défaut de clé ; Il peut être copié.
<br/><br/>
Vous pouvez également ouvrir le portail de contrôle d’accès. Ce portail de contrôle d’accès est identique à l’aide de l’option Active Directory dans le volet de navigation de gauche.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">Gérer votre Namespace d’ACS</a> fournit plus d’informations sur le portail de contrôle d’accès.</td>
</tr>
<tr>
<td><strong>Synchroniser les clés de</strong> dans le compte de stockage</td>
<td>Lorsque vous créez un compte de stockage, une clé primaire et la clé secondaire sont automatiquement créés. Ces clés de cryptage de contrôlent l’accès à votre compte de stockage. Votre BizTalk Service utilise automatiquement la clé primaire. <strong>Les clés de synchronisation</strong> permettent aux utilisateurs de basculer entre la clé primaire et la clé secondaire sans interrompre le BizTalk Service.
<br/><br/>
Par exemple, vous souhaitez que le BizTalk Service pour utiliser une nouvelle clé primaire pour le compte de stockage. Pour ce faire :
<br/><br/>
<ol>
<li>Sélectionnez votre BizTalk Service et sélectionner les <strong>Clés de la synchronisation</strong>. Sélectionnez la clé secondaire. Lorsque vous effectuez cette opération, le BizTalk Service démarre à l’aide de la clé secondaire.</li>
<li>Dans le portail Azure classique, sélectionnez votre compte de stockage et régénérer la clé primaire. N’oubliez pas que votre BizTalk Service à l’aide de la clé secondaire.</li>
<li>Sélectionnez votre BizTalk Service et sélectionner les <strong>Clés de la synchronisation</strong>. Maintenant, sélectionnez la clé primaire. Il s’agit de la nouvelle clé primaire vous régénéré.</li>
<li>Dans le portail Azure classique, sélectionnez votre compte de stockage et régénérer la clé secondaire.</li>
</ol>
<br/>
Ce processus est appelé « clés de substitution ». L’objectif est de permettre aux utilisateurs de basculer entre la clé primaire et la clé secondaire sans interrompre le BizTalk Service.</td>
</tr>

<tr>
<td><strong>Supprimer</strong> votre application</td>
<td>Votre BizTalk Service et déployés de tous les éléments sont supprimés.</td>
</tr>
</table>


## <a name="monitor"></a>Moniteur
Ne s’applique pas à l’édition gratuite.

Lorsque vous sélectionnez le nom de votre BizTalk Service, l’onglet Moniteur est disponible et affiche les éléments suivants :

##### <a name="metric-graph-displays-the-selected-performance-metrics"></a>Graphique de métrique : Affiche les métriques de performances sélectionné
Ces métriques fournissent des valeurs en temps réel sur l’état de santé du BizTalk Service. Vous choisissez les mesures de performances sont affichent. Un maximum de six mesures de performances peut être affiché simultanément. 

Vous pouvez également choisir les valeurs **relatives** ou **absolues** et la plage horaire **intervalle** des mesures qui sont affichés. 

##### <a name="to-remove-or-display-metrics-in-the-graph"></a>Pour supprimer ou afficher les mesures dans le graphique :
1. Sélectionnez l’onglet **Moniteur** .
2. Dans la barre des tâches, sélectionnez **Ajouter des mesures** :  
![Sélectionnez Ajouter des mesures][AddMetrics]
3. Vérifiez les métriques de performances que vous souhaitez afficher.
4. Activez la case à cocher pour revenir à l’onglet **Moniteur** .
5. Sélectionnez le cercle en regard de la mesure pour afficher de cette mesure dans le graphique.  

    Par exemple, la mesure de **l’Utilisation de l’UC** est grisée ; sa sortie n’est pas affichée dans le graphique :  
![Mesure de l’utilisation de l’UC est grisée.][GrayedMetric]  

    Sélectionnez le texte grisé en cercle pour activer la mesure de **L’utilisation du processeur** afficher sa sortie dans le graphique :  
![Mesure de l’utilisation de l’UC est activée.][EnabledMetric]

6. Pour supprimer une mesure à partir de l’affichage graphique et de la liste, sélectionnez **Supprimer** dans la barre des tâches. Pour rajouter le métrique pour la liste, sélectionnez **Ajouter des mesures** dans la barre des tâches, vérifiez la métrique et activez la case à cocher pour revenir à l’onglet **Moniteur** . Sélectionnez le texte grisé pour activer la mesure de cercle.

## <a name="Metrics"></a>Mesures disponibles
Les compteurs/métriques de performances suivants sont disponibles :

<table border="1">

<tr>
<td><strong>Latence de RountdTrip</strong></td>
<td>Affiche la durée moyenne en millisecondes (ms) pour traiter un message à partir du moment de que la réception du message jusqu'à ce que le message est entièrement traité par le BizTalk Service sur tous les ponts. Seuls les messages traités avec succès sont comptés.<br/><br/>
Lorsque les événements suivants se produisent, un cachet de date est créé :
<ul>
<li>Message d’entrée de la passerelle</li>
<li>Message est routé vers la destination</li>
<li>Réception de la réponse de destination</li>
<li>Réponse d’accusé de réception de destination envoyée à la passerelle</li>
</ul>
<br/>
Cette mesure montre le résultat du calcul suivant :
<br/><br/>
[Destination accusé de réception réponse envoyée à la passerelle] - [Message entre dans la passerelle]</td>
</tr>
<tr>
<td><strong>Échecs au niveau de la Source</strong></td>
<td>Affiche le nombre total de messages qui ont échoué par le BizTalk Service lors de l’extraction des messages depuis les points de terminaison source.</td>
</tr>
<tr>
<td><strong>Utilisation de l’UC</strong></td>
<td>Répertorie la moyen % temps processeur de toutes les instances de rôle.</td>
</tr>
<tr>
<td><strong>Latence de traitement</strong></td>
<td>Affiche la durée moyenne en millisecondes (ms) pour traiter un message par le BizTalk Service sur tous les ponts, à l’exclusion de la durée de destinations. Seuls les messages traités avec succès sont comptés.<br/><br/>
Lorsque chacun des événements suivants se produisent, un cachet de date est créé :

<ul>
<li>Message d’entrée de la passerelle</li>
<li>Message est routé vers la destination</li>
<li>Réception de la réponse de destination</li>
<li>Réponse d’accusé de réception de destination envoyée à la passerelle</li>
</ul>
<br/>Cette mesure montre le résultat du calcul suivant :<br/><br/>
[Destination accusé de réception réponse envoyée à la passerelle] - [Message entre dans la passerelle] - [Destination réponse] + [Message est routé vers la destination]</td>
</tr>
<tr>
<td><strong>Échecs de processus</strong></td>
<td>Affiche le nombre total de messages qui ont échoué au cours du traitement par le BizTalk Service sur tous les ponts dans un intervalle de temps.</td>
</tr>
<tr>
<td><strong>Messages envoyés</strong></td>
<td>Affiche le nombre total de messages envoyés par le BizTalk Service sur tous les ponts, dans un intervalle de temps. Cette mesure est incrémentée lorsqu’un message envoyé à partir d’un pipeline atteint la destination de l’itinéraire. Cette mesure n’indique pas qu’un message est traité avec succès.<br/><br/>
Dans un scénario de demande-réponse, la métrique est incrémentée lorsque la destination de l’itinéraire envoie un accusé de réception vers le pipeline.</td>
</tr>
<tr>
<td><strong>Messages reçus</strong></td>
<td>Affiche le nombre total de messages reçus par le BizTalk Service sur tous les ponts dans un intervalle de temps. Cette mesure est incrémentée lorsqu’un nouveau message est reçu par le pipeline.</td>
</tr>
<tr>
<td><strong>Messages dans le processus</strong></td>
<td>Affiche le nombre total de messages actuellement traités par le BizTalk Service dans un intervalle de temps.</td>
</tr>
<tr>
<td><strong>Messages traités</strong></td>
<td>Affiche le nombre total de messages traités avec succès par le BizTalk Service sur tous les ponts dans un intervalle de temps. Cette mesure est incrémentée lorsqu’un message est reçu correctement par le pipeline et correctement routé vers la destination.</td>
</tr>
</table>


## <a name="scale"></a>Échelle
Dans l’onglet échelle, vous pouvez ajouter ou soustraire le nombre d’unités utilisées par votre BizTalk Service. Par défaut, est une unité configurée. Unités supplémentaires peuvent être ajoutées à l’échelle de votre BizTalk Service. Lorsque vous augmentez l’échelle, vous augmentez le débit. La quantité de ressources augmente également, y compris les ponts déployés, les accords, les connexions du cœur de métier et de puissance de traitement. Par exemple, vous augmentez l’échelle à partir de le 1 unité à 2 unités. Dans ce cas, vous pouvez déployer double du nombre de ponts, doubler les accords, deux fois les connexions du cœur de métier et que double la puissance de traitement.

Certaines éditions de BizTalk n’offrent pas une option de redimensionnement. Dans ce cas, une unité est autorisée. Pour déterminer votre version peut être mise à l’échelle le nombre d’unités, reportez-vous à [les Services BizTalk : graphique des éditions de](biztalk-editions-feature-chart.md).

L’augmentation du nombre d’unités peut influencer le prix. Si vous augmentez les unités, **l’Enregistrer** la sélection affiche un message qui vous indique si la facturation est affectée. Ensuite, vous choisissez de continuer. Lorsque vous augmentez le nombre d’unités, le BizTalk Service statut devient actif mise à jour. Dans l’état de la mise à jour, votre BizTalk Service continue de s’exécuter.

[Les Services BizTalk : graphique des éditions de](biztalk-editions-feature-chart.md) définit une « unité ».


## <a name="configure"></a>Configurer
Ne s’applique pas aux connexions d’hybrides.

Définit l’état de sauvegarde sur aucune ou automatique. La valeur None, aucune sauvegarde est créés automatiquement. Lorsque cette propriété a la valeur automatique, configurez l’emplacement de sauvegarde, la fréquence de la sauvegarde et combien de temps conserver les fichiers de sauvegarde. 

[Les Services BizTalk : sauvegarde et restauration](biztalk-backup-restore.md) fournit les détails. 


## <a name="HybridConnections"></a>Connexions hybride
Hybride connexions une application Azure, comme les applications Web ou applications Mobile dans le Service d’application Azure, de connectent à une ressource sur site qui utilise un port TCP statique, tel que SQL Server, MySQL, API de Web HTTP et la plupart des Services Web personnalisés. Connexions d’hybrides sont gérées dans les Services BizTalk dans Azure portal classique.

Pour créer des connexions de hybride dans le Service d’application Azure, consultez [accès aux ressources à l’aide de connexions hybride dans Azure Application Service sur site](../app-service-web/web-sites-hybrid-connection-get-started.md).

Pour créer ou gérer des connexions hybride dans les Services BizTalk d’Azure, consultez [Connexions d’hybrides](integration-hybrid-connection-overview.md).



## <a name="next"></a>Suivant
Maintenant que vous êtes familiarisé avec les différents onglets, vous pouvez plus d’informations sur les fonctionnalités des Services de BizTalk Azure :

- [Les Services BizTalk : limitation](biztalk-throttling-thresholds.md)  
- [Les Services BizTalk : Nom de l’émetteur et la clé de l’émetteur](biztalk-issuer-name-issuer-key.md)  
- [Services de BizTalk : Sauvegarde et restauration](biztalk-backup-restore.md)

## <a name="see-also"></a>Voir aussi
- [Connexions hybride](integration-hybrid-connection-overview.md)  
- [Les Services BizTalk : Développeur, basique, Standard et Premium éditions graphique](biztalk-editions-feature-chart.md)  
- [Les Services BizTalk : Mise en service de portail de classique à l’aide de Azure](biztalk-provision-services.md)  
- [Services de BizTalk : Graphique de l’état du Service BizTalk](biztalk-service-state-chart.md)  
- [Comment faire démarrer à l’aide du SDK des Services BizTalk Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[QuickStart]: ./media/biztalk-dashboard-monitor-scale-tabs/QuickStartIcon.png
[AddMetrics]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_AddMetrics.png
[GrayedMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_GrayedMetric.png
[EnabledMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_EnabledMetric.png
 
