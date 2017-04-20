<properties
   pageTitle="Création d’un ticket de support pour SQL Data Warehouse | Microsoft Azure"
   description="Procédure de création d’un ticket de support dans l’entrepôt de données SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/01/2016"
   ms.author="sonyama;barbkess"/>

# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>Comment faire pour créer un ticket de support pour l’entrepôt de données SQL
 
Si vous rencontre des problèmes avec votre entrepôt de données SQL, veuillez créer une prise en charge de ticket afin que notre équipe technique peut vous aider.

## <a name="create-a-support-ticket"></a>Création d’un ticket de support

1. Ouvrez le [portail Azure][].

2. Dans l’écran d’accueil, cliquez sur la mosaïque **aide + prise en charge** .

    ![Aide + prise en charge](./media/sql-data-warehouse-get-started-create-support-ticket/help-support.png)

3. À l’aide de + lame de prise en charge, cliquez sur **demande de prise en charge de créer**.

    ![Nouvelle demande de support](./media/sql-data-warehouse-get-started-create-support-ticket/create-support-request.png)
    
    <a name="request-quota-change"></a> 

4. Sélectionnez le **Type de demande**.

    ![Type de demande](./media/sql-data-warehouse-get-started-create-support-ticket/request-type.png)
    
    >[AZURE.NOTE]  Par défaut, chaque serveur SQL (par exemple, myserver.database.windows.net) dispose d’un **Quota de DTU** de 45 000. Ce contingent est simplement une limite de sécurité. Vous pouvez augmenter votre quota par la création d’un ticket de support et en sélectionnant des *quotas* comme le type de demande. Pour calculer votre DTU a besoin, multipliez le 7.5 par total [que DWU][] nécessaire. Par exemple, vous souhaitez héberger des deux DW6000s sur un serveur SQL, puis vous devez demander un contingent DTU de 90 000.  Vous pouvez afficher votre consommation de courant DTU à partir de la lame de serveur SQL dans le portail. Interruption et reprises des bases de données sont comptabilisés dans le quota DTU. 

5. Sélectionnez l' **abonnement** qui héberge la base de données avec le problème que vous signalez.

    ![Abonnement](./media/sql-data-warehouse-get-started-create-support-ticket/subscription.png)

6. Sélectionnez la ressource **SQL Data Warehouse** .

    ![Ressources](./media/sql-data-warehouse-get-started-create-support-ticket/resource.png)

7. Sélectionnez votre [Azure prend en charge le plan][].

    - Support de **facturation, la gestion de quota et d’abonnement** est disponible à tous les niveaux de prise en charge.
    - Prise en charge de la **réparation** est fourni par [développeur][], [Standard][], [Professionnel Direct][] ou assistance [Premier][] . Réparation de problèmes sont les problèmes rencontrés par les clients lors de l’utilisation d’Azure dans le cas où il y a des raisons valables de croire que Microsoft a provoqué le problème.
    - **Encadrement de développeur** et de **services de conseils** sont disponibles sur les niveaux de prise en charge [Directe de professionnel][] et [Premier][] . 
    
    Si vous avez un Premier plan de prise en charge, vous pouvez également signaler SQL Data Warehouse des problèmes sur le [portail en ligne de Microsoft Premier][].  Pour en savoir plus sur les différents plans de support, y compris la portée, de temps de réponse, de tarification, etc., consultez [Azure prend en charge les plans][Azure prend en charge le plan] .  Pour les questions fréquemment posées sur Azure prend en charge, voir [Azure prend en charge les questions fréquentes][].  

    ![Plan de support](./media/sql-data-warehouse-get-started-create-support-ticket/support-plan.png)

8. Sélectionnez la **catégorie**et le **Type de problème** .

    ![Catégorie de type de problème](./media/sql-data-warehouse-get-started-create-support-ticket/problem-type-category.png)

9. Décrire le problème et choisir le niveau d’impact sur l’activité.

    ![Description du problème](./media/sql-data-warehouse-get-started-create-support-ticket/problem-description.png)

10. Vos **informations de contact** pour ce ticket de support sera rempli. Mettre à jour si nécessaire.

    ![Infos de contact](./media/sql-data-warehouse-get-started-create-support-ticket/contact-info.png)

11. Cliquez sur **créer** pour soumettre la demande de prise en charge.


## <a name="monitor-a-support-ticket"></a>Un ticket de support de moniteur

Une fois que vous avez soumis la demande de prise en charge, l’équipe de support Azure prendra contact avec vous. Pour vérifier vos informations et l’état de la demande, cliquez sur **Gérer les demandes de support** sur le tableau de bord.

![Vérification du statut](./media/sql-data-warehouse-get-started-create-support-ticket/check-status.png)

## <a name="other-resources"></a>Autres ressources

En outre, vous pouvez vous connecter à la Communauté SQL Data Warehouse de [Débordement de pile][] ou sur le [forum MSDN de magasin de données de SQL Azure][].

<!--Image references--> 

<!--Article references--> 
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units

<!--MSDN references--> 

<!--Other web references--> 
[Azure portal]: https://portal.azure.com/
[Plan de support Azure]: https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/  
[Développeur]: https://azure.microsoft.com/support/plans/developer/  
[Standard]: https://azure.microsoft.com/support/plans/standard/  
[Professionnel Direct]: https://azure.microsoft.com/support/plans/prodirect/  
[Premier]: https://azure.microsoft.com/support/plans/premier/  
[FAQ sur l’assistance Azure]: https://azure.microsoft.com/support/faq/
[Portail de Microsoft Premier online]: https://premier.microsoft.com/
[Débordement de pile]: https://stackoverflow.com/questions/tagged/azure-sqldw/
[Forum de MSDN de magasin de données SQL Azure]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/

