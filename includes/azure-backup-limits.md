 (sauvegarde des coffres-forts<properties
   pageTitle = Backu d’Azure »p limits table"
   Description = « décrit les limites système Azure Backup."
   services="backup"
   documentationCenter="NA"
   authors="Jim-Parker"
   manager="jwhit"
   editor="" />
<tags
   ms.service="backup"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="10/05/2015"
   ms.author="trinadhk";"jimpark"; "aashishr" />


Les limites suivantes s’appliquent à la sauvegarde d’Azure.

| Identificateur de la limite | Limite par défaut |
|---|---|
|Nombre de serveurs/machines qui peuvent être inscrits sur chaque coffre-fort|50 pour Windows serveur/Client/SCDPM <br/> 200 pour les machines virtuelles de IaaS|
|Taille de la source de données pour les données stockées dans le stockage Azure coffre-fort|54400 Go max<sup>1</sup>|
|Nombre de chambres fortes sauvegarde qui peuvent être créés dans chaque abonnement Azure|25 (coffres-forts de sauvegarde) <br/> Chambre forte de Services de récupération de 25 par région|
|Nombre de fois où backup peut être planifiée par jour|3 par jour pour le serveur/Client Windows <br/> 2 par jour pour SCDPM <br/> Une fois par jour pour les machines virtuelles de IaaS|
|Disques de données associés à une machine virtuelle Azure pour la sauvegarde|16|

- <sup>1</sup> La limite de 54400 Go ne s’applique pas à la sauvegarde de IaaS VM.

