<properties
    pageTitle="Modifier l’ID de clients clés coffre-fort après déplacement d’un abonnement | Microsoft Azure"
    description="Apprendre à passer l’ID de client pour un coffre-fort clé après qu’un abonnement est déplacé vers un autre client"
    services="key-vault"
    documentationCenter=""
    authors="amitbapat"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/13/2016"
    ms.author="ambapat"/>

# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>Modifier un ID de clients clés coffre-fort après déplacement d’un abonnement
### <a name="q-my-subscription-was-moved-from-tenant-a-to-tenant-b-how-do-i-change-the-tenant-id-for-my-existing-key-vault-and-set-correct-acls-for-principals-in-tenant-b"></a>Q : mon abonnement a été déplacé à partir de clients A au client B. Comment modifier l’ID client pour mon coffre-fort de clé existant et définir des ACL correct pour les entités clients B ?

Lorsque vous créez un nouveau coffre-fort de clé dans un abonnement, elle est automatiquement liée à l’ID de client Azure Active Directory par défaut pour cet abonnement. Toutes les entrées de stratégie d’accès sont également liées à ce code de client. Lorsque vous déplacez votre abonnement Azure à partir de clients A au client B, vos coffres-forts de clé existantes sont inaccessibles par les identités (utilisateurs et applications) dans le client B. Pour résoudre ce problème, vous devez :

- Modification de l’ID de client associé à tous les coffres-forts clés existants dans cet abonnement au client B.
- Supprimer toutes les entrées de stratégie d’accès.
- Ajouter de nouvelles entrées de stratégie d’accès qui sont associées aux clients B.

Par exemple, si vous avez un coffre-fort clé 'myvault' dans un abonnement qui a été déplacé à partir de client A à B, ici du locataire comment modifier le code client pour ce coffre-fort de clés et de supprimer les anciennes stratégies d’accès.

<pre>
$vaultResourceId = (get-AzureRmKeyVault - VaultName myvault). ResourceId $vault = Get-AzureRmResource – ResourceId $vaultResourceId - ExpandProperties $vault. Properties.TenantId = (Get-AzureRmContext). Tenant.TenantId $vault. Properties.AccessPolicies = @() Set-AzureRmResource - ResourceId $vaultResourceId-propriétés $vault. Propriétés
</pre>

Dans la mesure où ce coffre-fort lors du locataire A avant le déplacement, la valeur d’origine de **$vault. Properties.TenantId** est un locataire, lors de la **(Get-AzureRmContext). Tenant.TenantId** est client B.

Maintenant que votre coffre-fort est associé à l’ID de client correct et suppression des anciennes entrées de stratégie d’accès, définir l’accès de nouvelles entrées de stratégie au [Jeu-AzureRmKeyVaultAccessPolicy](https://msdn.microsoft.com/library/mt603625.aspx).

## <a name="next-steps"></a>Étapes suivantes

Si vous avez des questions sur Azure clé coffre-fort, visitez les [Forums de coffre-fort clé Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
