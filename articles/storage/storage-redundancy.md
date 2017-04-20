<properties
    pageTitle="La réplication du stockage Azure | Microsoft Azure"
    description="Dans votre compte de stockage Microsoft Azure sont répliquées de durabilité et de haute disponibilité. Options de réplication incluent le stockage redondant localement (LRS), stockage redondant zone (ZRS), géo-redondances (GRS) et stockage geo redondant d’accès en lecture (RA-GRS)."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="tamram"/>

# <a name="azure-storage-replication"></a>Réplication du stockage Azure

Les données de votre compte de stockage Microsoft Azure sont toujours répliquées pour garantir la haute disponibilité et durabilité. La réplication copie vos données, au sein du même centre de données, soit à un deuxième centre de données, selon l’option de réplication que vous choisissez. La réplication protège vos données et préserve votre application le temps de fonctionnement en cas de défaillance du matériel en régime transitoire. Si vos données sont répliquées vers un deuxième centre de données, également pour la protection de vos données contre une défaillance irrémédiable dans l’emplacement principal.

La réplication garantit que votre compte de stockage remplit le [Contrat de niveau de Service (SLA) pour le stockage](https://azure.microsoft.com/support/legal/sla/storage/) de même face à des échecs. Consultez que le contrat SLA pour plus d’informations sur le stockage Azure garanties de durabilité et de disponibilité. 

Lorsque vous créez un compte de stockage, vous pouvez sélectionner une des options de réplication suivantes :  

- [Stockage redondant localement (LRS)](#locally-redundant-storage)
- [Stockage redondant de zone (ZRS)](#zone-redundant-storage)
- [Stockage redondant geo (GRS)](#geo-redundant-storage)
- [Accès en lecture de stockage redondant geo (GRS-RA)](#read-access-geo-redundant-storage)

Accès en lecture de stockage redondant geo (RA-GRS) est l’option par défaut lorsque vous créez un nouveau compte de stockage.

Le tableau suivant fournit une vue d’ensemble rapide des différences entre les LRS, ZRS, GRS et RA-GRS, tandis que les sections suivantes traitent de chaque type de réplication plus en détail.

| Stratégie de réplication                                                               | LRS | ZRS | GRS | RA-GRS |
|:----------------------------------------------------------------------------------|:---|:---|:---|:------|
| Données sont répliquées sur plusieurs centres de données.                                     | N°  | Oui | Oui | Oui    |
| Données peuvent être lues à partir de l’emplacement secondaire et l’emplacement principal. | N°  | N°  | N°  | Oui    |
| Nombre de copies de données conservées dans des nœuds distincts.                             | 3   | 3   | 6   | 6      |

Informations pour les options de redondance différents de tarification, consultez [Tarification de stockage Azure](https://azure.microsoft.com/pricing/details/storage/) .

>[AZURE.NOTE] Stockage de prime prend en charge le stockage uniquement localement redondant (LRS). Pour plus d’informations sur le stockage de la prime, consultez [prime stockage : stockage hautes performances pour les charges de travail de Machine virtuelle Azure](storage-premium-storage.md).

## <a name="locally-redundant-storage"></a>Stockage redondant localement

Stockage redondant localement (LRS) réplique les données trois fois dans une unité d’échelle de stockage qui est hébergée dans un centre de données dans la région dans laquelle vous avez créé votre compte de stockage. Une demande d’écriture réussit uniquement une fois qu’il a été écrit dans tous les réplicas de trois. Ces trois répliques résident dans des domaines d’erreur distinct et mise à niveau des domaines au sein de l’unité de stockage d’une échelle. 

Une unité d’échelle de stockage est un ensemble de racks de nœuds de stockage. Un domaine d’erreur (FD) est un groupe de nœuds qui représentent une unité physique de défaillance et qui peuvent être considérés comme des nœuds appartenant au même rack physique. Un domaine de mise à niveau (UD) est un groupe de nœuds qui sont mis à jour ensemble au cours du processus d’une mise à niveau de service (déploiement). Trois réplicas sont répartis sur les UDs et FDs au sein d’une unité d’échelle de stockage afin de garantir que les données sont disponibles même si défaillance matérielle affecte un seul rack ou lorsque les nœuds sont mis à niveau au cours d’un déploiement. 

LRS est l’option la moins coûteuse et offre moins de durabilité par rapport aux autres options. En cas d’incident au niveau du centre de données (incendie, inondation etc.) tous les trois répliques peuvent être perdue ou irrécupérable. Pour atténuer ce risque géographique redondants stockage (GRS) est recommandé pour la plupart des applications.

Stockage redondant localement peut toujours être souhaitable dans certains scénarios : 

- Fournit la plus grande bande passante maximale des options de réplication de stockage Azure.

- Si votre application stocke les données qui peuvent être facilement recréées, vous pouvez opter pour LRS.

- Certaines applications sont limitées à la réplication des données uniquement dans un pays en raison des exigences de gouvernance des données. Une région appariée peut être dans un autre pays ; Veuillez consulter [les régions Azure](https://azure.microsoft.com/regions/) pour plus d’informations sur les paires de la région.

## <a name="zone-redundant-storage"></a>Stockage redondant de zone

Stockage redondant de zone (ZRS) réplique vos données en mode asynchrone sur les centres de données dans une ou deux régions en plus du stockage trois répliques semblables à LRS, fournissant ainsi une durabilité élevée que LRS. Les données stockées dans ZRS sont durables, même si le centre de données principal est indisponible ou non récupérable.
Les clients qui envisagent d’utiliser ZRS Sachez que : 

- ZRS n’est disponible que pour les objets BLOB de bloc dans les comptes de stockage polyvalente et il est pris en charge uniquement dans les versions de service de stockage 2014-02-14 et ultérieures. 

- Dans la mesure où la réplication asynchrone implique un retard, en cas de sinistre local, il est possible que les modifications qui n’ont pas encore été répliquées sur le site secondaire seront perdues si les données ne peuvent pas être récupérées à partir de l’ordinateur principal.

- Le réplica n’est peut-être pas disponible jusqu'à ce que Microsoft lance le basculement vers le secondaire.

- Comptes ZRS Impossible de convertir ultérieurement en LRS ou GRS. De la même façon, un compte existant de LRS ou de GRS ne peut pas être converti à un compte ZRS.

- Les comptes ZRS n’ont pas de mesures ou la fonction de journalisation. 

## <a name="geo-redundant-storage"></a>Stockage redondant geo

Stockage redondant geo (GRS) réplique vos données sur une zone secondaire des centaines de miles en dehors de la zone primaire. Si votre compte de stockage a GRS activée, vos données sont durables, même dans le cas d’une panne régionale complète ou un sinistre dans lequel la région principale n’est pas récupérable.

Pour un compte de stockage avec GRS activé, une mise à jour est tout d’abord engagé à la région principale, où elle est répliquée trois fois. La mise à jour est ensuite répliquée en mode asynchrone sur la zone secondaire, où elle est également répliquée trois fois. 

Avec GRS les zones primaires et secondaires gérer des duplications entre domaines d’erreur distinct et mise à niveau des domaines au sein d’une unité d’échelle de stockage comme indiqué avec LRS.

Considérations relatives à :

- Dans la mesure où la réplication asynchrone implique un retard, en cas de sinistre régional, il est possible que les modifications qui n’ont pas encore été répliquées sur la zone secondaire seront perdues si les données ne peuvent pas être récupérées à partir de la zone primaire.

- Le réplica n’est pas disponible à moins que Microsoft lance le basculement vers la zone secondaire.

- Si une application souhaite lire à partir de la zone secondaire, que l’utilisateur doit activer RA-GRS. 

Lorsque vous créez un compte de stockage, vous sélectionnez la région principale pour le compte. La zone secondaire est déterminée en fonction de la région principale et ne peut pas être modifiée. Le tableau suivant indique les appariements de zone primaire et secondaire.

| Principal             | Secondaire           |
|---------------------|---------------------|
| États-Unis centre nord    | États-Unis centre sud    |
| États-Unis centre sud    | États-Unis centre nord    |
| Les États-Unis             | États-Unis Ouest             |
| États-Unis Ouest             | Les États-Unis             |
| États-Unis East 2           | États-Unis centre          |
| États-Unis centre          | États-Unis East 2           |
| Europe du Nord        | Europe de l’ouest         |
| Europe de l’ouest         | Europe du Nord        |
| Asie du Sud-est     | Asie de l’est           |
| Asie de l’est           | Asie du Sud-est     |
| Chine orientale          | En Chine du Nord         |
| En Chine du Nord         | Chine orientale          |
| Nord-est du Japon          | Ouest du Japon          |
| Ouest du Japon          | Nord-est du Japon          |
| Sud du Brésil        | États-Unis centre sud    |
| Est de l’Australie      | Sud-est de l’Australie |
| Sud-est de l’Australie | Est de l’Australie      |
| Sud de l’Inde         | Central de l’Inde       |
| Central de l’Inde       | Sud de l’Inde         |
| US Gov Iowa         | US Gov Virginie     |
| US Gov Virginie     | US Gov Iowa         |
| Canada Central      | Est du Canada          |
| Est du Canada         | Canada Central      |
| Ouest du Royaume-Uni             | Sud du Royaume-Uni            |
| Sud du Royaume-Uni            | Ouest du Royaume-Uni             |
| Central de l’Allemagne     | Nord-est de l’Allemagne   |
| Nord-est de l’Allemagne   | Central de l’Allemagne     |
| États-Unis Ouest 2           | États-Unis Ouest centre     |
| États-Unis Ouest centre     | États-Unis Ouest 2           |

Pour obtenir des informations sur les régions pris en charge par Azure, consultez [Régions d’Azure](https://azure.microsoft.com/regions/).
 
## <a name="read-access-geo-redundant-storage"></a>Stockage redondant geo d’accès en lecture

Stockage geo redondant d’accès en lecture (RA-GRS) optimise la disponibilité de votre compte de stockage, en fournissant un accès en lecture seule aux données dans l’emplacement secondaire, en plus de la réplication entre les deux régions de GRS. 

Lorsque vous activez l’accès en lecture seule à vos données de la zone secondaire, vos données sont disponibles sur un point de terminaison secondaire, en plus du point de terminaison principal pour votre compte de stockage. Le point de terminaison secondaire est identique au point de terminaison principal, mais ajoute le suffixe `–secondary` pour le nom du compte. Par exemple, si votre principal point de terminaison pour le service d’objet Blob est `myaccount.blob.core.windows.net`, votre point de terminaison secondaire est `myaccount-secondary.blob.core.windows.net`. Les touches d’accès rapide de votre compte de stockage sont les mêmes pour les deux points de terminaison principales et secondaires.

Considérations relatives à :

- Votre application doit gérer le point de terminaison interagit avec lors de l’utilisation de RA-GRS. 

- RA-GRS est destiné à des fins de haute disponibilité. Pour des conseils de l’évolutivité, veuillez consulter [liste de contrôle de performances](storage-performance-checklist.md).

## <a name="next-steps"></a>Étapes suivantes

- [Prix du stockage Azure](https://azure.microsoft.com/pricing/details/storage/)
- [À propos des comptes de stockage Azure](storage-create-storage-account.md)
- [Évolutivité du stockage Azure et les objectifs de Performance](storage-scalability-targets.md)
- [Options de redondance de stockage Microsoft Azure et géographique de l’accès en lecture de stockage redondant](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)  
- [SOSP papier - stockage Azure : Un nuage hautement disponible Service de stockage grâce à la cohérence renforcée](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)  
