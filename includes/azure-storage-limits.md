Ressources|Limite par défaut
---|---
Nombre de comptes de stockage par abonnement|200<sup>1</sup>
To par compte de stockage|500 TO
Nombre maximal de conteneurs blob, BLOB, partages de fichiers, tables, files d’attente, entités ou messages par compte de stockage|Seule limite est la capacité de compte de stockage de 500 To
Taille maximale du conteneur de l’objet blob unique, d’une table ou d’une file d’attente|500 TO
Max le nombre de blocs dans un blob de bloc ou d’ajouter des blob|50 000
Taille d’un bloc dans un blob de bloc maximale ou ajouter des blob|4 MO
Taille d’un objet blob de bloc maximale ou ajouter des blob|50 000 x 4 Mo (environ 195 Go) 
Taille maximale d’un objet blob de page |1 TO
Taille maximale d’une entité de table|1 MO
Nombre maximal de propriétés dans une entité de table|252
Taille maximale d’un message dans une file d’attente|64 KO
Taille maximale d’un partage de fichier|5 TO
Taille maximale d’un fichier dans un partage de fichiers|1 TO
Nombre maximal de fichiers dans un partage de fichiers|Seule limite est la capacité totale de 5 To du partage de fichier
Max 8 Ko par seconde partage|1000
Nombre maximal de fichiers dans un partage de fichiers|Seule limite est la capacité totale de 5 To du partage de fichier
Nombre maximal de conteneurs blob, BLOB, partages de fichiers, tables, files d’attente, entités ou messages par compte de stockage|Seule limite est la capacité de compte de stockage de 500 To
Nombre maximal de stratégies d’accès stockées par conteneur, de partage de fichiers, de table ou de file d’attente|5
Total taux de demandes (en supposant que la taille de l’objet 1 Ko) par compte de stockage|Jusqu'à 20 000 IOPS, entités par seconde ou messages par seconde
Débit cible pour les blob unique|Jusqu'à 60 Mo par seconde, ou jusqu'à 500 demandes par seconde
Débit cible pour une file d’attente unique (messages 1 Ko)|Jusqu'à 2000 des messages par seconde
Débit cible pour la partition d’une table unique (entités de 1 Ko)|Entités de 2000 par seconde
Débit cible pour le partage de fichier unique|Jusqu'à 60 Mo par seconde
De pénétration de max<sup>2</sup> par compte de stockage (nous régions)|10 Gbits/s si activé de GRS/ZRS<sup>3</sup> , 20 Gbps pour LRS
De sortie max<sup>2</sup> par compte de stockage (nous régions)|Gbit/20 s si RA-GRS/GRS/ZRS<sup>3</sup> activé, 30 Gbps pour LRS
De pénétration de max<sup>2</sup> par compte de stockage (européenne et régions d’Asie de l’est)|Gbit/5 s si activé de GRS/ZRS<sup>3</sup> , 10 Gbits/s pour LRS
De sortie max<sup>2</sup> par compte de stockage (européenne et régions d’Asie de l’est)|10 Gbits/s si RA-GRS/GRS/ZRS<sup>3</sup> activé, Gbit/15 s pour LRS

<sup>1</sup> Cela inclut les comptes de stockage Standard et Premium. Si vous avez besoin de plus de 200 comptes de stockage, effectuer une demande via la [Prise en charge d’Azure](https://azure.microsoft.com/support/faq/). L’équipe de stockage Azure examinera votre argumentaire et peut approuver jusqu'à 250 comptes de stockage. 

<sup>2</sup> *Entrée* fait référence à toutes les données (requêtes) envoyées à un compte de stockage. *Sortie* fait référence à toutes les données (réponses) à partir d’un compte de stockage en cours de réception.  

<sup>3</sup> Options de réplication de stockage Azure sont les suivantes :

- **RA-GRS**: stockage redondant geo d’accès en lecture. Si-RA GRS est activée, des cibles de sortie pour l’emplacement secondaire sont identiques à celles de l’emplacement principal.
- **GRS**: stockage redondant Geo. 
- **ZRS**: stockage redondant-Zone. Disponible uniquement pour les objets BLOB de bloc. 
- **LRS**: stockage redondant localement. 

