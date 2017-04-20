<properties 
    pageTitle="Tâches autorisées dans différentes régions ou états dans les Services BizTalk | Microsoft Azure" 
    description="Les actions/opérations autorisées dans l’état MABS : arrêter, Démarrer, redémarrer, suspendre, reprendre, supprimer, mettre à l’échelle, mise à jour de la configuration et sauvegarde des" 
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



# <a name="biztalk-services-service-state-chart"></a>Les Services BizTalk : Graphique d’état Service
Selon l’état actuel du service BizTalk, il y a des opérations que vous pouvez ou ne pouvez pas exécuter le service BizTalk.

Par exemple, vous devez configurer un nouveau service BizTalk dans Azure portal classique. Lorsqu’elle est terminée avec succès, le service BizTalk est dans un état actif. Dans l’état actif, vous pouvez arrêter le service BizTalk. Si l’arrêt est réussie, le service BizTalk passe à l’état arrêté. En cas d’échec de l’arrêt, le service BizTalk passe à un état de StopFailed. Dans l’état StopFailed, vous pouvez redémarrer le service BizTalk. Si vous tentez une opération qui n’est pas autorisée, tels que de reprendre le service BizTalk, l’erreur suivante se produit :

**Opération non autorisée**

Pour configurer un BizTalk Service, accédez à [les Services BizTalk : portail classique de mise en service des Azure à l’aide de](http://go.microsoft.com/fwlink/p/?LinkID=302280).

Les tableaux suivants répertorient les opérations ou les actions qui peuvent être effectuées lorsque le BizTalk Service est dans un état spécifique. Un ✔ signifie que l’opération est autorisée dans cet état. Une entrée vide signifie que l’opération ne peut pas être effectuée dans cet état.

## <a name="start-stop-restart-suspend-resume-and-delete-operations"></a>Démarrer, arrêter, redémarrer, suspendre, reprendre, opérations et de suppression
<table border="1">
<tr>
        <th colspan="15">Action ou opération</th>
</tr>

<tr>
        <th rowspan="18">État du Service BizTalk</th>
</tr>
<tr bgcolor="FAF9F9">
        <th> </th>
        <th>Début</th>
        <th>Arrêter</th>
        <th>Redémarrage</th>
        <th>Suspendre</th>
        <th>Resume</th>
        <th>Supprimer</th>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Active</b></td>
<td> </td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Désactivé</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Suspendu</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Arrêté</b></td>
<td><center>✔</center></td>
<td> </td>
<td><center>✔</center></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Echec de la mise à jour du service</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>DisableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>EnableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>StartFailed<br/>
StopFailed<br/>
RestartFailed</b></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>SuspendedFailed<br/>
ResumeFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>CreatedFailed<br/>
RestoreFailed<br/></b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ConfigUpdateFailed</b></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ScaleFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
</table>
<br/>

## <a name="scale-update-configuration-and-backup-operations"></a>Échelle, la Configuration de la mise à jour et les opérations de sauvegarde
<table border="1">
<tr>
        <th colspan="15">Action ou opération</th>
</tr>

<tr>
        <th rowspan="18">État du Service BizTalk</th>
</tr>
<tr bgcolor="FAF9F9">
        <th> </th>
        <th>Échelle</th>
        <th>Configuration de la mise à jour</th>
        <th>Sauvegarde</th>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Active</b></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Désactivé</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Suspendu</b></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Arrêté</b></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Echec de la mise à jour du service</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>DisableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>EnableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>StartFailed<br/>
StopFailed<br/>
RestartFailed</b></td>
<td> </td>
<td><center>✔</center></td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>SuspendedFailed<br/>
ResumeFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>CreatedFailed<br/>
RestoreFailed<br/></b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ConfigUpdateFailed</b></td>
<td> </td>
<td><center>✔</center></td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ScaleFailed</b></td>
<td><center>✔</center></td>
<td> </td>
<td> </td>
</tr>
</table>

## <a name="see-also"></a>Voir aussi
- [Les Services BizTalk : Mise en service de portail de classique à l’aide de Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [Services de BizTalk : Onglets de tableau de bord, du moniteur et échelle](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [Les Services BizTalk : Développeur, basique, Standard et Premium éditions graphique](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [Services de BizTalk : Sauvegarde et restauration](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [Les Services BizTalk : limitation](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
- [Les Services BizTalk : Nom de l’émetteur et la clé de l’émetteur](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
- [Comment faire démarrer à l’aide du SDK des Services BizTalk Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)


 
