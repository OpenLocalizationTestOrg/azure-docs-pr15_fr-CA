
# <a name="azure-and-internet-of-things"></a>Azure et l’Internet des objets

Bienvenue dans Microsoft Azure et l’Internet des objets (IoT). Cet article présente une architecture de solution IoT qui décrit les caractéristiques communes d’une solution IoT que vous déployez à l’aide des services Azure. IoT solutions nécessitent la sécuriser, la communication bidirectionnelle entre les périphériques, numérotation éventuellement des millions et un solution back-end. Par exemple, un back-end de solution peut utiliser analytique prédictive, automatisée, mettre en évidence des perspectives de votre flux d’événement de périphérique-nuage.

Azure IoT concentrateur est un bloc de construction clé lors de l’implémentation de cette architecture de solution IoT à l’aide des services Azure. IoT Suite offre complètes, de bout en bout, les implémentations de cette architecture pour des scénarios spécifiques IoT. Par exemple : 

- La solution de *surveillance à distance* vous permet de surveiller l’état des périphériques tels que les distributeurs. 
- La solution de *gestion prédictive* vous aide à anticiper les besoins de gestion de périphériques tels que des pompes des stations de pompage à distance et à éviter les temps d’arrêt imprévus.

## <a name="iot-solution-architecture"></a>Architecture de la solution IoT

Le diagramme suivant montre une architecture de solution IoT classique. Le diagramme n’inclut pas les noms de tous les services Azure spécifiques, mais il décrit les éléments clés dans une architecture de solution IoT générique. Dans cette architecture, les périphériques IoT collectent les données qu’ils envoient à une passerelle de nuage. La passerelle de nuage rend les données disponibles pour le traitement par d’autres services back-end d’où les données sont remises à d’autres applications métier du ou des opérateurs humains via un tableau de bord ou tout autre périphérique de présentation.

![Architecture de la solution IoT][img-solution-architecture]

> [AZURE.NOTE] Pour une présentation détaillée de l’architecture de IoT, reportez-vous à l' [Architecture de référence Microsoft Azure IoT][lnk-refarch].

### <a name="device-connectivity"></a>Connectivité des périphériques

Dans cette architecture de solution IoT, périphériques envoient de télémétrie, telles que les lectures des capteurs à partir d’une station de pompage, à un point de terminaison du nuage pour le traitement et le stockage. Dans un scénario de maintenance PREVENTIVE, le back-end peut utiliser le flux de données de capteur pour déterminer quand une pompe spécifique requiert une maintenance. Périphériques peuvent également recevoir et répondre aux commandes du nuage vers le périphérique en lisant des messages à partir d’un point de terminaison du nuage. Par exemple, dans le cas d’une maintenance PREVENTIVE le back-end de solution peut envoyer des commandes aux autres pompes dans la station de pompage pour commencer le réacheminement des flux juste avant que la maintenance est devant démarrer pour vous assurer que l’ingénieur de maintenance peut commencer lorsqu’il arrive.

Un des principaux défis IoT projets est comment vous connecter les périphériques pour le back-end de solution fiable et sécurisée. IoT périphériques présentent des caractéristiques différentes par rapport à d’autres clients tels que les navigateurs et les applications mobiles. Périphériques IoT :

- Sont souvent des systèmes intégrés avec aucun opérateur humain.
- Peut être déployé dans des emplacements distants, où l’accès physique est coûteuse.
- Peut uniquement être accessible par le biais de la dorsale de la solution. Il n’y a aucun autre moyen d’interagir avec le périphérique.
- Peut avoir limité des ressources de traitement et d’alimentation.
- Peut avoir une connectivité réseau intermittente, lente ou coûteuse.
- Devrez peut-être utiliser des protocoles d’application spécifique à l’industrie, propriétaire ou personnalisé.
- Peuvent être créés à l’aide d’un large éventail de plates-formes de matériel et les logiciels les plus courants.

Outre les exigences ci-dessus, toute solution IoT doit aussi offrir évolutivité, de sécurité et de fiabilité. Le jeu de spécifications de connectivité est long et complexe à implémenter à l’aide des technologies traditionnelles telles que les conteneurs du web et de messagerie courtiers. Concentrateur de IoT Azure et les kits de développement de périphérique IoT rendent plus facile à mettre en œuvre des solutions qui répondent à ces exigences.

Un périphérique peut communiquer directement avec un point de terminaison de passerelle de nuage, ou si le périphérique ne peut pas utiliser les protocoles de communication qui prend en charge de la passerelle de nuage, il peut se connecter via une passerelle intermédiaire. Par exemple, la [passerelle de protocole Azure IoT] [ lnk-protocol-gateway] pouvez traduire protocole si les périphériques ne peut pas utiliser un protocole qui prend en charge le concentrateur de IoT.

### <a name="data-processing-and-analytics"></a>Traitement des données et analytique

Dans le nuage, un IoT solution back end est où la plupart du traitement des données se produit, par exemple le filtrage et agrégation de télémétrie et de la router vers d’autres services. La solution IoT back end :

- Reçoit la télémétrie à grande échelle de vos périphériques et détermine le traitement et le stockage de ces données. 
- Peut vous permettre d’envoyer des commandes à partir du cloud pour un périphérique spécifique.
- Fournit des fonctionnalités d’inscription qui permettent aux périphériques de disposition et au contrôle les périphériques qui sont autorisés à se connecter à votre infrastructure.
- Vous permet de suivre l’état de vos périphériques et de surveiller leurs activités.

Dans le cas d’une maintenance PREVENTIVE, le back-end de solution stocke les données de télémétrie historiques. Le back-end peut utiliser ces données à utiliser pour identifier des comportements susceptibles d’indiquer la maintenance est dû à une pompe spécifique.

IoT solutions peuvent inclure des boucles de rétroaction automatique. Par exemple, un module analytique dans le back-end peut identifier de télémétrie la température d’un périphérique spécifique situé au-dessus des niveaux d’exploitation normaux. La solution peut alors envoyer une commande au périphérique, demander au programme d’exécuter une action corrective.

### <a name="presentation-and-business-connectivity"></a>Connectivité de métier et de présentation

La couche métier et de présentation de connectivité permet aux utilisateurs d’interagir avec la solution IoT et les périphériques. Il permet aux utilisateurs d’afficher et d’analyser les données collectées à partir de leurs périphériques. Ces vues peuvent prendre la forme de tableaux de bord ou rapports BI qui peuvent afficher des données historiques ou à proximité des données en temps réel. Par exemple, un opérateur peut vérifier l’état de la station de pompage particulier et voir les alertes déclenchées par le système. Cette couche permet également l’intégration de la IoT solution back-end avec les applications existantes de métier à lier à des processus d’entreprise ou de flux de travail. Par exemple, la solution de gestion prédictive peut intégrer un système de planification que la documentation d’un ingénieur pour visiter une station de pompage lorsque la solution identifie une pompe nécessitant une maintenance.

![Tableau de bord de solution IoT][img-dashboard]

[img-solution-architecture]: ./media/iot-azure-and-iot/iot-reference-architecture.png
[img-dashboard]: ./media/iot-azure-and-iot/iot-suite.png

[lnk-machinelearning]: http://azure.microsoft.com/documentation/services/machine-learning/
[Azure IoT Suite]: http://azure.microsoft.com/solutions/iot
[lnk-protocol-gateway]:  ../articles/iot-hub/iot-hub-protocol-gateway.md
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
