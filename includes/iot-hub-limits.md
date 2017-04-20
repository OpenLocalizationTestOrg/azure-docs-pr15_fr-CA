Le tableau suivant répertorie les limites associées aux niveaux de service différents (S1, S2, S3, F1). Pour plus d’informations sur le coût de chaque *unité* dans chaque couche, consultez [Tarification de concentrateur IoT](https://azure.microsoft.com/pricing/details/iot-hub/).

| Ressources | S1 Standard | S2 Standard | S3 Standard | F1 libre |
| -------- | ----------- | ----------- | ----------- | ------- |
| Messages par jour | 400 000 | 6,000,000   | 300,000,000 | 8 000   |
| Capacité maximale | 200    | 200         | 200         | 1       |

> [AZURE.NOTE] Si vous envisagez l’utilisation de plus de 200 unités avec un concentrateur de couche S1 ou S2 ou S3, veuillez contacter le support technique de Microsoft.

Le tableau suivant répertorie les limites qui s’appliquent aux ressources de IoT Hub :

| Ressources | Limite de |
| -------- | ----- |
| Maximum payé concentrateurs IoT par abonnement Azure | 10 |
| Concentrateurs IoT libres maximum par abonnement Azure | 1 |
| Nombre maximal d’identités des appareils<br/>  renvoyé dans un appel unique | 1000 |
| Conservation maximale IoT concentrateur de messages de périphérique-nuage | 7 derniers jours |
| Taille maximale du message du périphérique-nuage | 256 KO |
| Taille maximale de lot du périphérique-nuage | 256 KO |
| Nombre maximal de messages dans un lot de périphérique-nuage | 500 |
| Taille maximale du message du nuage vers le périphérique | 64 KO |
| Durée de vie maximale pour les messages du nuage vers le périphérique | 2 jours |
| Nombre maximal de remise pour le nuage-appareil <br/> messages | 100 |
| Nombre maximal de remise pour les messages de feedback <br/> en réponse à un message de nuage-appareil | 100 |
| Durée de vie maximale pour les messages de feedback dans <br/> réponse à un message de nuage-appareil | 2 jours |

> [AZURE.NOTE] Si vous avez besoin de plus de 10 moyeux de IoT payé dans un abonnement Azure, veuillez contacter le support technique de Microsoft.

Le service IoT concentrateur limite les demandes cas de dépassement des contingents suivants :

| Manette des gaz | Valeur de par-hub |
| -------- | ------------- |
| Opérations de Registre identité <br/> (créer, récupérer, répertorier, mettre à jour, supprimer), <br/> importation/exportation en bloc ou individuellement | min/5000/unité (pour S3) <br/> min/100/unité (pour S1 et S2). |
| Connexions du périphérique | s/6000/unité (pour S3), 120/s/unité (pour (S2), s/12/unité (pour S1). <br/>Minimum de 100 par seconde. |
| Périphérique-nuage envoie | s/6000/unité (pour S3), 120/s/unité (pour (S2), s/12/unité (pour S1). <br/>Minimum de 100 par seconde. |
| Nuage-DISPOSITIF envoie | min/5000/unité (pour S3), 100/min/unité (pour S1 et S2). |
| Nuage-DISPOSITIF reçoit | min/50000/unité (pour S3), 1000/min/unité (pour S1 et S2). |
| Opérations de téléchargement de fichier | téléchargement de fichiers de 5000 notifications/min/unité (pour S3), téléchargement de fichier 100 notifications/min/unité (pour S1 et S2). <br/> 10000 SAS URI peut être à la fois pour un compte de stockage Azure.<br/> URI SAS 10/périphérique peut être à la fois des. |
