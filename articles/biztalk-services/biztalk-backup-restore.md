<properties 
    pageTitle="Création et restauration d’une sauvegarde dans les Services BizTalk | Microsoft Azure" 
    description="Les Services BizTalk comprend la sauvegarde et restauration. Apprenez à créer et restaurer une sauvegarde et déterminer ce qui est sauvegardé. MABS, WABS" 
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
    ms.date="08/15/2016" 
    ms.author="mandia"/>


# <a name="biztalk-services-backup-and-restore"></a>Services de BizTalk : Sauvegarde et restauration

Les Services BizTalk Azure inclut les fonctionnalités de sauvegarde et de restauration. Cette rubrique décrit comment sauvegarder et restaurer des Services BizTalk en utilisant le portail classique Azure.

Vous pouvez également sauvegarder des Services BizTalk à l’aide de l' [API REST de Services BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325584). 

> [AZURE.NOTE] Hybride connexions ne sont pas sauvegardées, quel que soit l’édition. Vous devez recréer vos connexions hybride.

## <a name="before-you-begin"></a>Avant de commencer

- Sauvegarde et restauration n’est peut-être pas disponible pour toutes les éditions. Consultez [Services BizTalk : graphique des éditions de](biztalk-editions-feature-chart.md).

- À l’aide du portail classique Azure, vous pouvez créer une sauvegarde à la demande ou créer une sauvegarde planifiée. 

- Contenu de la sauvegarde peut être restaurée au même BizTalk Service ou d’un BizTalk Service. Pour restaurer le BizTalk Service en utilisant le même nom, le BizTalk Service existant doit être supprimé et le nom doit être disponible. Après la suppression d’un BizTalk Service, il peut prendre plus de temps que voulu pour le même nom soit disponible. Si vous ne pouvez pas attendre le même nom soit disponible, puis de restaurer vers un nouveau Service de BizTalk.

- Les Services BizTalk peut être restaurés à la même édition ou une édition supérieure. Restaurez les Services BizTalk sur une édition inférieure, à partir de laquelle la sauvegarde a été établie, n'est pas pris en charge.

    Par exemple, une sauvegarde à l’aide de l’édition de base peut être restaurée à l’édition Premium. Impossible de restaurer une sauvegarde à l’aide de l’édition Premium à l’Édition Standard.

- Les numéros de contrôle de l’EDI sont sauvegardés pour maintenir la continuité des numéros de contrôle. Si les messages sont traités après la dernière sauvegarde, la restauration de cet sauvegarde contenu peut provoquer numéros de contrôle dupliqués.

- Si un lot de messages actives, traite le lot **avant** d’exécuter une sauvegarde. Lors de la création d’une sauvegarde (comme nécessaires ou planifiée), les messages dans les lots ne sont jamais stockés. 

    **Si une sauvegarde est effectuée avec des messages actives dans un lot, ces messages ne sont pas sauvegardées et sont donc perdues.**

- Facultatif : Dans le portail de Services BizTalk, arrêter les opérations de gestion.


## <a name="create-a-backup"></a>Créer une sauvegarde

Une sauvegarde peut être prise à tout moment et est entièrement contrôlée par vous. Cette section répertorie les étapes pour créer des sauvegardes à l’aide du portail classique Azure, y compris :

[Sur la sauvegarde à la demande](#backupnow)

[Planifier une sauvegarde](#backupschedule)

#### <a name="backupnow"></a>Sur la sauvegarde à la demande
1. Dans le portail Azure classique, sélectionnez les **Services BizTalk**et puis sélectionnez le BizTalk Service que vous voulez sauvegarder.
2. Dans l’onglet **tableau de bord** , sélectionnez **Sauvegarder** au bas de la page.
3. Entrez un nom de sauvegarde. Par exemple, entrez *myBizTalkService*BU*Date*.
4. Choisissez un compte de stockage blob, puis sélectionnez la case à cocher pour démarrer la sauvegarde.

Une fois la sauvegarde terminée, un conteneur avec le nom de sauvegarde que vous entrez est créé dans le compte de stockage. Ce conteneur contient la configuration de votre sauvegarde de BizTalk Service.

#### <a name="backupschedule"></a>Planifier une sauvegarde

1. Dans le portail Azure classique, sélectionnez les **Services BizTalk**, sélectionnez le nom de BizTalk Service pour planifier la sauvegarde et puis sélectionnez l’onglet **configurer** .
2. Définir l' **état de la sauvegarde** **automatique**. 
3. Sélectionnez le **Compte de stockage** pour stocker la sauvegarde, entrez la **fréquence** pour créer les sauvegardes et combien de temps conserver les sauvegardes (**Rétention jours**) :

    ![][AutomaticBU]

    **Notes**   
    - Dans la **Période de conservation**, la période de rétention doit être supérieure à la fréquence de sauvegarde.
    - Sélectionnez **toujours conserver au moins une sauvegarde**, même si elle est au-delà de la période de rétention.
    

4. Cliquez sur **Enregistrer**.


Lorsqu’une sauvegarde planifiée s’exécute, il crée un conteneur (pour stocker les données de sauvegarde) dans le compte de stockage que vous avez entré. Le nom du conteneur est appelé *BizTalk Service nom-date-heure*. 

Si le tableau de bord de BizTalk Service affiche un état **d’Échec** :

![Dernier état de la sauvegarde planifiée][BackupStatus] 

Le lien ouvre l’opération des journaux Services de gestion pour vous aider à résoudre les problèmes. Consultez [Services BizTalk : résolution des problèmes liés à l’aide des journaux de l’opération](http://go.microsoft.com/fwlink/p/?LinkId=391211).

## <a name="restore"></a>Restauration

Vous pouvez restaurer les sauvegardes à partir du portail classique Azure, ou à partir de [Restauration d’API REST Service BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325582). Cette section répertorie les étapes pour restaurer l’utilisation du portail classique.

#### <a name="before-restoring-a-backup"></a>Avant de restaurer une sauvegarde

- Nouveau suivi, d’archivage et de surveillance des banques peuvent être entrés lors de la restauration d’un BizTalk Service.

- Les mêmes données de Runtime de EDI sont restaurées. La sauvegarde de l’EDI Runtime stocke les numéros de contrôle. Les numéros de contrôle restaurés sont dans l’ordre à partir du moment de la sauvegarde. Si les messages sont traités après la dernière sauvegarde, la restauration de cet sauvegarde contenu peut provoquer numéros de contrôle dupliqués.

#### <a name="restore-a-backup"></a>Restauration d’une sauvegarde

1. Dans le portail Azure classique, sélectionnez **Nouveau** > **Application Services** > **BizTalk Service** > **restaurer**:

    ![Restauration d’une sauvegarde][Restore]

2. Dans **URL de sauvegarde**, sélectionnez l’icône de dossier et le compte de stockage Azure qui stocke la sauvegarde de la configuration du BizTalk Service. Développez le conteneur, puis dans le volet de droite, sélectionnez le fichier .txt de sauvegarde correspondant. 
<br/><br/>
Cliquez sur **Ouvrir**.

3. Dans la page **Restaurer le BizTalk Service** , entrez un **Nom de Service BizTalk** et vérifiez que **l’URL du domaine**, **Édition**et **région** pour le BizTalk Service restaurée. **Créer une nouvelle instance de la base de données SQL** pour la base de données de suivi :

    ![][RestoreBizTalkService]

    Cliquez sur la flèche suivante.

4.  Vérifiez le nom de la base de données SQL, entrez le serveur physique dans laquelle sera créée la base de données SQL et un nom d’utilisateur/mot de passe pour ce serveur.


    Si vous souhaitez configurer l’édition de base de données SQL, la taille et autres propriétés, sélectionnez **Configurer les paramètres de base de données avancées**. 

    Cliquez sur la flèche suivante.

5. Créer un nouveau compte de stockage, ou entrez un compte de stockage existants pour le BizTalk Service.

7. Activez la case à cocher pour lancer la restauration.

Une fois la restauration terminée, un nouveau BizTalk Service est répertorié dans un état suspendu dans la page Services BizTalk dans Azure portal classique.



### <a name="postrestore"></a>Après la restauration d’une sauvegarde

Le BizTalk Service est toujours restauré dans un état **suspendu** . Dans cet état, vous pouvez modifier toute configuration avant que le nouvel environnement est opérationnel, y compris :

- Si vous avez créé des applications de BizTalk Service en utilisant le SDK des Services BizTalk Azure, vous devrez mettre à jour les informations d’identification de contrôle d’accès (ACS) dans ces applications de fonctionner avec l’environnement restauré.

- Vous restaurez un BizTalk Service pour répliquer un environnement de BizTalk Service existant. Dans ce cas, s’il existe des accords configurés dans le portail Services BizTalk d’origine qui utilisent un dossier FTP du code source, vous devez mettre à jour les accords dans l’environnement nouvellement restaurée à utiliser un autre dossier code source FTP. Dans le cas contraire, il peut exister deux accords différents tente d’extraire le même message.

- Si vous avez restauré pour plusieurs environnements de BizTalk Service, assurez-vous que vous ciblez l’environnement approprié dans les applications de Visual Studio, applets de commande PowerShell, autres API ou API des OM de gestion des partenaires commerciaux.

- Il est recommandé de configurer des sauvegardes automatiques sur l’environnement BizTalk Service nouvellement restaurée.

Pour démarrer le BizTalk Service dans Azure portal classique, sélectionnez le BizTalk Service restaurée et sélectionnez **reprendre** dans la barre des tâches. 



## <a name="what-gets-backed-up"></a>Ce qui est sauvegardé

Lorsqu’une sauvegarde est créée, les éléments suivants sont sauvegardés :

<table border="1"> 
<tr bgcolor="FAF9F9">
<th> </th>
<TH>Articles sauvegardés</TH> 
</tr> 
<tr>
<td colspan="2">
 <strong>Portail de Services BizTalk Azure</strong></td>
</tr> 
<tr>
<td>Configuration et exécution</td> 
<td>
<ul>
<li>Détails du partenaire et de profil</li>
<li>Accords de partenariat</li>
<li>Déployé des assemblys personnalisés</li>
<li>Ponts déployés</li>
<li>Certificats</li>
<li>Transformations déployées</li>
<li>Pipelines</li>
<li>Modèles créés et enregistrés dans le portail de Services de BizTalk</li>
<li>X12 ST01 et GS01 de mappages</li>
<li>Numéros de contrôle (EDI)</li>
<li>Valeurs AS2 Message MIC</li>
</ul>
</td>
</tr> 
 
<tr>
<td colspan="2">
 <strong>Service BizTalk Azure</strong></td>
</tr> 
<tr>
<td>Certificat SSL</td> 
<td>
<ul>
<li>Données de certificat SSL</li>
<li>Mot de passe de certificat SSL</li>
</ul>
</td>
</tr> 
<tr>
<td>Paramètres du Service de BizTalk</td> 
<td>
<ul>
<li>Nombre d’unités échelle</li>
<li>Édition</li>
<li>Version du produit</li>
<li>Région/Centre de données</li>
<li>Clé et espace de noms access Control Service (ACS)</li>
<li>Chaîne de connexion de base de données de suivi</li>
<li>Chaînes de connexion de compte de stockage d’archivage</li>
<li>Analyse des chaînes de connexion de compte de stockage</li>
</ul>
</td>
</tr> 
<tr>
<td colspan="2">
 <strong>Éléments supplémentaires</strong></td>
</tr> 
<tr>
<td>Base de données de suivi</td> 
<td>Lorsque le BizTalk Service est créé, les détails de la base de données de suivi sont entrées, y compris le serveur de base de données SQL Azure et le nom de la base de données de suivi. La base de données de suivi n’est pas automatiquement sauvegardée.
<br/><br/>
<strong>Important</strong><br/>
Si la base de données de suivi est supprimée et les besoins de la base de données récupérées, une sauvegarde précédente doit exister. Si une sauvegarde n’existe pas, la base de données de suivi et ses données ne sont pas récupérables. Dans ce cas, créez une nouvelle base de données de suivi portant le même nom de base de données. Geo-réplication est recommandée.</td>
</tr> 
</table>

## <a name="next"></a>Suivant

Pour créer des Services de BizTalk Azure dans Azure portal classique, accédez à [les Services BizTalk : portail classique de mise en service des Azure à l’aide de](http://go.microsoft.com/fwlink/p/?LinkID=302280). Pour démarrer la création d’applications, consultez [Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## <a name="see-also"></a>Voir aussi
- [Service de sauvegarde de BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325584)
- [Restaurer le Service BizTalk à partir de la sauvegarde](http://go.microsoft.com/fwlink/p/?LinkID=325582)
- [Les Services BizTalk : Développeur, basique, Standard et Premium éditions graphique](http://go.microsoft.com/fwlink/p/?LinkID=302279)
- [Les Services BizTalk : Mise en service de portail de classique à l’aide de Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)
- [Services de BizTalk : Graphique de l’état de mise en service](http://go.microsoft.com/fwlink/p/?LinkID=329870)
- [Services de BizTalk : Onglets de tableau de bord, du moniteur et échelle](http://go.microsoft.com/fwlink/p/?LinkID=302281)
- [Les Services BizTalk : limitation](http://go.microsoft.com/fwlink/p/?LinkID=302282)
- [Les Services BizTalk : Nom de l’émetteur et la clé de l’émetteur](http://go.microsoft.com/fwlink/p/?LinkID=303941)
- [Comment faire démarrer à l’aide du SDK des Services BizTalk Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[BackupStatus]: ./media/biztalk-backup-restore/status-last-backup.png
[Restore]: ./media/biztalk-backup-restore/restore-ui.png
[AutomaticBU]: ./media/biztalk-backup-restore/AutomaticBU.png
[RestoreBizTalkService]: ./media/biztalk-backup-restore/RestoreBizTalkServiceWindow.png
 
