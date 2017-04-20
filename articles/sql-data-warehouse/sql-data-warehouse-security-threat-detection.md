<properties
   pageTitle="Mise en route de détection de menace de magasin de données SQL"
   description="La mise en route avec la détection de la menace"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/24/2016"
   ms.author="lodipalm;sonyama;barbkess"/>


# <a name="get-started-with-threat-detection"></a>Mise en route de la détection des menaces

> [AZURE.SELECTOR]
- [L’audit](sql-data-warehouse-auditing-overview.md)
- [Détection de la menace](sql-data-warehouse-security-threat-detection.md)

## <a name="overview"></a>Vue d’ensemble

Détection des menaces détecte des activités anormales de la base de données indiquant les menaces de sécurité potentielles à la base de données. Une détection des menaces est en aperçu et est pris en charge pour l’entrepôt de données SQL.

Détection des menaces fournit une nouvelle couche de sécurité, ce qui permet aux clients de détecter et répondre aux menaces potentielles qu’ils se produisent en fournissant des alertes de sécurité sur les activités anormales. Les utilisateurs peuvent Explorer les événements suspects à l’aide [d’Audit de magasin de données SQL Azure](sql-data-warehouse-auditing-overview.md) afin de déterminer si elles résultent d’une tentative d’accéder, de violation ou d’exploiter des données dans le data warehouse.
Détection des menaces facile à des menaces potentielles à l’entrepôt de données sans avoir besoin d’être un expert en sécurité ou de gérer des systèmes de surveillance de la sécurité avancée.

Par exemple, la détection des menaces détecte certaines activités anormales de la base de données indiquant les éventuelles tentatives d’injection SQL. L’injection SQL est un des problèmes de sécurité Web application sur Internet, utilisée pour attaquer des applications pilotées par les données courantes. Les intrus tirent parti de vulnérabilités des applications d’injecter des instructions SQL malveillantes dans des champs de saisie d’application, de violation ou de modification des données dans la base de données.


## <a name="set-up-threat-detection-for-your-database"></a>Configurer la détection des menaces pour votre base de données

1. Lancer le portail Azure à [https://portal.azure.com](https://portal.azure.com).

2. Accédez à la lame de configuration de l’entrepôt de données SQL que vous souhaitez surveiller. Dans la lame de paramètres, sélectionnez **audit et détection des menaces**.

    ![Volet de navigation][1]

3. Activer la lame de configuration de **l’audit et la détection des menaces** **sur** l’audit, qui affiche les paramètres de détection de menace.

    ![Volet de navigation][2]

4. Activer la détection des menaces **sur** .

5. Configurer la liste des e-mails qui recevra les alertes de sécurité lors de la détection des activités d’entrepôt de données anormales.

6. Cliquez sur **Enregistrer** dans la lame de configuration **détection d’audit et de menace** pour enregistrer le nouveau ou mis à jour l’audit et la stratégie de détection des menaces.

    ![Volet de navigation][3]


## <a name="explore-anomalous-data-warehouse-activities-upon-detection-of-a-suspicious-event"></a>Explorez les activités d’entrepôt de données anormales lors de la détection d’un événement suspect

1. Vous recevrez une notification par courrier électronique lors de la détection des activités d’anormal de la base de données. <br/>
Le message fournit des informations sur l’événement de sécurité suspects, y compris de la nature des activités anormales, nom de la base de données, nom du serveur et l’heure de l’événement. En outre, il fournit des informations sur les causes possibles et les mesures recommandées pour analyser et limiter la menace potentielle à la base de données.<br/>

    ![Volet de navigation][4]

2. Dans l’e-mail, cliquez sur le lien de **Journal d’audit de SQL Azure** , qui lance le portail classique Azure et afficher les enregistrements d’audit pertinentes au moment de l’événement suspect.

    ![Volet de navigation][5]

3. Cliquez sur les enregistrements d’audit pour afficher des détails sur les activités suspectes de la base de données comme instruction SQL IP client et de motif de panne.

    ![Volet de navigation][6]

4. De la lame d’enregistrements d’audit, cliquez sur **Ouvrir dans Excel** pour ouvrir un préconfiguré excel le modèle pour importer et exécutez une analyse plus approfondie du journal d’audit autour de l’heure de l’événement suspect.<br/>
**Remarque :** Dans Excel 2010 ou version ultérieure, requête d’alimentation et le paramètre **Combiner rapide** est requis

    ![Volet de navigation][7]

5. Pour configurer le paramètre **Combiner rapide** - dans l’onglet de ruban de **Requête de l’alimentation** , sélectionnez les **Options** pour afficher la boîte de dialogue Options. Sélectionnez la section confidentialité et choisissez la deuxième option - « Ignorer les niveaux de confidentialité et potentiellement améliorer les performances » :

    ![Volet de navigation][8]

6. Pour charger les journaux d’audit SQL, vérifiez que les paramètres dans les paramètres d’onglet sont correctement définis et puis sélectionnez le ruban de « Données » et cliquez sur le bouton 'Actualiser tout'.

    ![Volet de navigation][9]

7. Les résultats apparaissent dans la feuille de **Journaux d’Audit de SQL** qui vous permet d’exécuter une analyse plus approfondie des activités anormales qui ont été détectés et atténuer l’impact de l’événement de sécurité dans votre application.


<!--Image references-->
[1]: ./media/sql-data-warehouse-security-threat-detection/1_td_click_on_settings.png
[2]: ./media/sql-data-warehouse-security-threat-detection/2_td_turn_on_auditing.png
[3]: ./media/sql-data-warehouse-security-threat-detection/3_td_turn_on_threat_detection.png
[4]: ./media/sql-data-warehouse-security-threat-detection/4_td_email.png
[5]: ./media/sql-data-warehouse-security-threat-detection/5_td_audit_records.png
[6]: ./media/sql-data-warehouse-security-threat-detection/6_td_audit_record_details.png
[7]: ./media/sql-data-warehouse-security-threat-detection/7_td_audit_records_open_excel.png
[8]: ./media/sql-data-warehouse-security-threat-detection/8_td_excel_fast_combine.png
[9]: ./media/sql-data-warehouse-security-threat-detection/9_td_excel_parameters.png
