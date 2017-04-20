<properties 
    pageTitle="En savoir plus sur la limitation dans les Services BizTalk | Microsoft Azure" 
    description="Obtenir des informations sur les seuils de limitation et entraînant des comportements d’exécution pour les Services BizTalk. La limitation est basée sur l’utilisation de la mémoire et le nombre de messages. MABS, WABS" 
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





# <a name="biztalk-services-throttling"></a>Les Services BizTalk : limitation

Les Services BizTalk Azure implémente en fonction de deux conditions de limitation de service : utilisation de la mémoire et le nombre de messages simultanés de traitement. Cette rubrique répertorie les seuils de limitation et décrit le comportement d’exécution lorsqu’une condition de limitation se produit.

## <a name="throttling-thresholds"></a>Seuils d’accélération

Le tableau suivant répertorie la limitation de la source et les seuils :

||Description|Seuil inférieur|Seuil élevé|
|---|---|---|---|
|Mémoire|% de la mémoire disponible totale du système/PageFileBytes. <p><p>PageFileBytes de disponible totale est d’environ 2 fois la RAM du système.|60 %|70 %|
|Traitement des messages|Nombre de messages à traiter simultanément|40 * nombre de cœurs|100 * nombre de cœurs|

Lorsqu’un seuil élevé est atteint, Azure BizTalk Services commence à limiter. La limitation s’arrête quand le seuil bas est atteint. Par exemple, votre service utilise 65 % de mémoire système. Dans ce cas, le service ne limite pas. Démarrage de votre service à l’aide de la mémoire système de 70 %. Dans ce cas, le service limite et continue de gaz jusqu'à ce que le service utilise la mémoire de système (seuil inférieur) de 60 %.

Les Services BizTalk Azure effectue le suivi de l’état de limitation (état normal et l’état accélérée) et la limitation de la durée.


## <a name="runtime-behavior"></a>Comportement à l’exécution

Lorsque les Services BizTalk Azure entre dans un état de limitation, les éléments suivants se produisent :

- La limitation est par instance de rôle. Par exemple :<br/>
Limitation de la RoleInstanceA. RoleInstanceB n’est pas la limitation. Dans ce cas, les messages dans RoleInstanceB sont traités comme prévu. Messages dans RoleInstanceA sont ignorés et échouent avec l’erreur suivante :<br/><br/>
**Le serveur est occupé. Essayez à nouveau.**<br/><br/>
- Les sources d’extraction ne pas interroger ou téléchargez un message. Par exemple :<br/>
Un pipeline extrait les messages à partir d’une source FTP externe. Il obtient l’instance de rôle effectuant l’extraction dans un état de limitation. Dans ce cas, le pipeline arrête le téléchargement des messages supplémentaires jusqu'à ce que l’instance de rôle arrête la régulation.
- Une réponse est envoyée au client afin que le client peut renvoyer le message.
- Vous devez attendre jusqu'à ce que la limitation est résolue. En particulier, vous devez attendre jusqu'à ce que le seuil bas est atteint.

## <a name="important-notes"></a>Remarques importantes
- La régulation ne peut pas être désactivée.
- Seuils de limitation ne peut pas être modifié.
- La limitation est mis en œuvre à l’échelle du système.
- Le serveur de base de données SQL Azure a également intégré la limitation.

## <a name="additional-azure-biztalk-services-topics"></a>Rubriques supplémentaires de Services de BizTalk Azure

-  [L’installation des Services BizTalk Azure SDK](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
-  [Didacticiels : Les Services BizTalk Azure](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
-  [Comment faire démarrer à l’aide du SDK des Services BizTalk Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
-  [Services BizTalk Azure](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## <a name="see-also"></a>Voir aussi
- [Les Services BizTalk : Développeur, basique, Standard et Premium éditions graphique](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [Les Services BizTalk : Mise en service de portail de classique à l’aide de Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [Services de BizTalk : Graphique de l’état de mise en service](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [Services de BizTalk : Onglets de tableau de bord, du moniteur et échelle](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [Services de BizTalk : Sauvegarde et restauration](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [Les Services BizTalk : Nom de l’émetteur et la clé de l’émetteur](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
 
