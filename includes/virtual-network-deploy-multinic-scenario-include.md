## <a name="scenario"></a>Scénario

Ce document vous guident à travers un déploiement qui utilise plusieurs cartes réseau dans des ordinateurs virtuels dans un scénario spécifique. Dans ce scénario, vous avez deux niveaux IaaS la charge de travail hébergé dans Azure. Chaque niveau est déployé sur son propre sous-réseau dans un réseau virtuel (VNet). La couche de front-end est composée de plusieurs serveurs web, regroupés dans un équilibreur de charge pour une disponibilité élevée. La couche de back-end est composée de plusieurs serveurs de base de données. Ces serveurs de base de données seront déployés avec deux cartes réseau, une pour l’accès de base de données, l’autre pour la gestion. Le scénario comprend également les groupes de sécurité réseau (NSGs) pour contrôler le trafic autorisé sur chaque sous-réseau et carte d’interface réseau dans le déploiement. La figure ci-dessous illustre l’architecture de base de ce scénario.  

![Scénario de MultiNIC](./media/virtual-network-deploy-multinic-scenario-include/Figure1.png)

