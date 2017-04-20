## <a name="peering-across-subscriptions"></a>Homologation sur les abonnements

Dans ce scénario, vous allez créer une homologation entre deux VNets appartenant à différents abonnements.

![sous-scénario croisée](./media/virtual-networks-create-vnetpeering-scenario-crosssub-include/figure01.PNG)

Homologation de VNet repose sur le contrôle d’accès basé sur les rôles (RBAC) pour l’autorisation. Pour le scénario de cross-abonnements, vous devez d’abord l’octroi d’autorisations suffisantes pour les utilisateurs qui va créer le lien d’homologation :

> [AZURE.NOTE] Si l’utilisateur dispose du privilège sur les deux abonnements, vous pouvez ignorer les étapes 1 à 4 ci-après.
