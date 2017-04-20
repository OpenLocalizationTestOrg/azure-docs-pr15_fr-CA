<properties
   pageTitle="Transférer la propriété d’un abonnement Azure | Microsoft Azure"
   description="Le transfert d’un abonnement Azure à un autre utilisateur et certains fréquemment posées (FAQ) sur le processus"
   services=""
   documentationCenter=""
   authors="genlin"
   manager="stevenpo"
   editor=""
   tags="billing,top-support-issue"/>

<tags
   ms.service="billing"
   ms.workload="na"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="10/10/2016"
   ms.author="genli"/>

# <a name="transferring-ownership-of-an-azure-subscription"></a>Transférer la propriété d’un abonnement Azure

Vraiment :

- Besoin de main sur la facturation de possession de votre abonnement Azure à quelqu'un d’autre ?
- Vous souhaitez modifier le compte utilisé pour vous inscrire pour Azure ? Vous peut-être utilisé votre Account de Microsoft, mais destiné à utiliser votre travail ou à l’école à la place de compte ?
- Vous souhaitez déplacer votre abonnement Azure à partir d’un répertoire vers un autre ?
- Azure et Office 365 dans différents locataires et de consolidation ?

Vous pouvez maintenant le faire facilement dans le Microsoft Azure compte Centre - pour paiement, MSDN, Microsoft Action Pack ou BizSpark abonnements.  Nous avons ajouté la possibilité de transférer votre abonnement vers un autre utilisateur. En d’autres termes, vous pouvez maintenant modifier l’admin. compte de tout paiement, MSDN, Microsoft Action Pack ou BizSpark abonnement dont vous êtes propriétaire, quel que soit le pays que vous travaillez dans. Nous prennent désormais en charge le transfert d’Azure Marketplace achats pour ces types d’abonnements.

> [AZURE.NOTE] Pour modifier votre abonnement à une offre différente, reportez-vous à la section [Switch votre abonnement Azure pour une autre offre](billing-how-to-switch-azure-offer.md) pour plus d’informations. Si vous avez besoin d’obtenir de l’aide à tout moment dans cet article, veuillez [contacter le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir votre problème résolu rapidement.

## <a name="how-to-transfer-ownership-of-an-azure-subscription"></a>Comment faire pour transférer la propriété d’un abonnement Azure

> [AZURE.VIDEO transfer-an-azure-subscription]

1.  Connexion à <https://account.windowsazure.com/Subscriptions>. Vous devez être l’administrateur du compte pour effectuer un transfert de propriété. Pour plus d’informations sur la façon de savoir qui est l’administrateur du compte de l’abonnement, consultez le [Forum aux questions](#faq).

2.  Sélectionnez l’abonnement pour le transfert.

3.  Cliquez sur l’option de **Transfert de l’abonnement** .

    ![Onglet d’abonnements compte Azure](./media/billing-subscription-transfer/image1.png)

4.  Suivez les invites pour spécifier le destinataire.

    ![Boîte de dialogue abonnement de transfert](./media/billing-subscription-transfer/image2.PNG)

5.  Le destinataire obtient automatiquement un e-mail avec un lien d’acceptation.

    ![E-mail de transfert d’abonnement au destinataire](./media/billing-subscription-transfer/image3.png)

6.  Le destinataire clique sur le lien et suit les instructions, y compris comment entrer leurs informations de paiement.

    ![Page web de premier abonnement transfert](./media/billing-subscription-transfer/image4.png)

    ![Page web de transfert deuxième abonnement](./media/billing-subscription-transfer/image5.png)

7. Succès ! L’abonnement est maintenant transférée.

<a id="faq"></a>
## <a name="frequently-asked-questions-faq"></a>Forum aux questions (FAQ)

-   **Comment puis-je savoir qui est l’administrateur du compte de l’abonnement ?**

    Vous pouvez vérifier qui est l’administrateur du compte de l’abonnement comme suit :

    1. Connectez-vous au [portail Azure](https://portal.azure.com).
    2. Dans le menu de concentrateur, sélectionnez **l’abonnement**.
    3. Sélectionner l’abonnement que vous souhaitez vérifier, puis sélectionnez **paramètres**.
    4. Sélectionnez **Propriétés**. L’administrateur du compte de l’abonnement apparaît dans la zone **Compte Admin** .  

-   **Un transfert de l’abonnement entraîne en aucune interruption de service ?**

    Il n’y a aucun impact sur le service. Cela efficacement annule l’abonnement sous le compte administrateur et crée un nouveau sous le compte du destinataire, mais associe les services Azure sous-jacent le nouvel abonnement. L’ID d’abonnement reste le même.

-   **Comment utiliser ce mécanisme pour changer le répertoire d’abonnement ?**-   
    Un abonnement Azure est créé dans le répertoire auquel appartient le compte d’administrateur. Ainsi, pour changer le répertoire, simplement transférer l’abonnement à un compte d’utilisateur dans le répertoire cible. Lorsque cet utilisateur termine la procédure d’accepter le transfert, l’abonnement déplace automatiquement vers le répertoire cible.

-   **Si je prends possession de facturation d’un abonnement à partir d’une autre organisation, ils continue à avoir accès à mes ressources ?**

    Si l’abonnement est transférée à un autre client, les utilisateurs associés au locataire précédent perdra l’accès à l’abonnement. Même si un utilisateur n’est pas un administrateur du Service ou le Co-admin plus, peut toujours avoir accès à l’abonnement par le biais des autres mécanismes de sécurité. Celles-ci comprennent :
    - Certificats de gestion qui accordent des droits d’administrateur sur les ressources d’abonnement de l’utilisateur. Pour plus d’informations, voir [créer et télécharger un certificat de gestion pour Azure](https://msdn.microsoft.com/library/azure/gg551722.aspx)
    -   Touches d’accès rapide pour les services comme stockage. Pour plus d’informations, reportez-vous à la section [Affichage, la copie et stockage de régénérer les touches d’accès](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)
    -   Des informations d’accès à distance pour les services tels que des ordinateurs virtuels Azure

    Il ne s’agit pas d’une liste complète. Le destinataire doit envisager aucun secret associé au service s’ils ont besoin restreindre l’accès à leurs ressources de mise à jour. La plupart des ressources peuvent être mis à jour comme suit :

    1.   Accédez au portail Azure : [ *https://portal.azure.com*](https://portal.azure.com)

    2.    Cliquez sur Parcourir tous les -&gt; toutes les ressources

    3.    Sélectionnez la ressource. La lame de ressources s’ouvre.

    4.    De la lame de la ressource, cliquez sur **paramètres**. Ici, vous pouvez afficher et mettre à jour les secrets existants.


-   **Si transférer l’abonnement au milieu du cycle de facturation, est le destinataire pour la facturation complète du cycle de paie ?**

    L’expéditeur est responsable du paiement de toute utilisation a été signalée jusqu’au point que le transfert est terminé. Le destinataire est responsable de l’utilisation dans le temps de transfert à partir de. Il peut y avoir une utilisation qui a eu lieu avant le transfert, mais a été signalée par la suite. Elle figurera dans la nomenclature de l’écran du destinataire.

-   **Le destinataire a-t-il accès à l’utilisation et l’historique de facturation**

    À ce stade, la seule information a révélé au destinataire est le montant de la dernière échéance (ou le solde actuel, si l’abonnement a été transféré avant la première lettre a été généré). L’abonnement ne sont pas transférés au reste de l’utilisation et l’historique de facturation.

-   **L’offre peut être modifiée lors d’un transfert ?**

    L’offre doit rester le même. Pour modifier votre offre, vous devez [contacter le support technique](http://go.microsoft.com/fwlink/?LinkID=619338).

-   **Puis-je transférer un abonnement à un compte d’utilisateur dans un autre pays ?**

    Non, pour l’instant, que cela n’est pas pris en charge. Compte d’utilisateur du destinataire doit être dans le même pays.

-   **Le destinataire peut utiliser d’un mécanisme de paiement différent ?**

    Oui. Il existe des limitations ici : maintenant l’abonnement de l’historique de facturation est répartie sur les deux comptes. Mais l’avantage est que vous pouvez le faire sans avoir à [contacter le support technique](http://go.microsoft.com/fwlink/?LinkID=619338).

-   **Le mode de paiement être affecté une fois que j’ai transféré un abonnement Azure ?**

    Pour accepter un transfert d’abonnement, carte de crédit ou paiement similaire doit être fourni à payer pour l’abonnement. Par exemple, si Bob transfère un abonnement à Jeanne et Jeanne accepte le transfert, Jane doit également fournir une méthode de paiement qu’elle utilisera pour régler l’abonnement. Une fois le transfert terminé, Bob n’est plus être débités de l’abonnement, qu'il est transféré à Jeanne.

## <a name="next-steps-after-accepting-ownership-of-a-subscription"></a>Étapes suivantes après avoir accepté la possession d’un abonnement

1. Vous êtes l’administrateur du compte. Passez en revue et mettre à jour l’administrateur du Service et des administrateurs. Gérer les administrateurs dans [Azure portal classique](https://manage.windowsazure.com) à partir de paramètres. [En savoir plus](http://go.microsoft.com/fwlink/?LinkID=533293).
2. Vous pouvez également utiliser le contrôle d’accès basé sur les rôles (RBAC) pour votre abonnement et les services. Visitez [portail Azure](https://portal.azure.com) [en savoir plus sur RBAC](http://go.microsoft.com/fwlink/?LinkID=544802)
3. Mettre à jour les informations d’identification avec les services de cet abonnement. Celles-ci comprennent :
    - Certificats de gestion qui accordent des droits d’administrateur sur les ressources d’abonnement de l’utilisateur. Pour plus d’informations, consultez [Création et téléchargement d’une gestion de certificats pour Azure](https://msdn.microsoft.com/library/azure/gg551722.aspx)
    -   Touches d’accès rapide pour les services comme stockage. Pour plus d’informations, reportez-vous à la section [Affichage, la copie et stockage de régénérer les touches d’accès](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)
    -   Des informations d’accès à distance pour les services tels que des ordinateurs virtuels Azure
4. Mettre à jour des alertes de facturation pour cet abonnement, au [Centre du compte Azure](https://account.windowsazure.com/Subscriptions)  [en savoir plus](http://go.microsoft.com/fwlink/?LinkID=533292)
5.  Si vous travaillez avec un partenaire, envisagez la mise à jour de l’ID de partenaire pour cet abonnement. Pour cela, dans le [Centre de compte Azure](https://account.windowsazure.com/Subscriptions).

> [AZURE.NOTE] Si vous en avez d’autres questions, veuillez [contacter le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir votre problème résolu rapidement.
