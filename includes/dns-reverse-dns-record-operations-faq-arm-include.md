<BR> 
## <a name="faq---reverse-dns-for-your-azure-assigned-ip-address"></a>Forum aux questions - DNS inversée pour votre adresse IP affectée d’Azure

### <a name="how-much-do-reverse-dns-records-cost"></a>Combien inverse les coûts des enregistrements DNS ?
C’est gratuit !  Il n’existe aucun coût supplémentaire pour les enregistrements DNS inversées ou des requêtes.

### <a name="will-the-reverse-dns-records-for-my-azure-assigned-public-ip-address-resolve-from-the-internet"></a>Résoudre les enregistrements DNS inverses pour mon adresse d’IP publique affectée d’Azure à partir d’internet ?
Oui. Une fois que vous définissez la propriété DNS inverse pour votre adresse IP publique, Azure gère toutes les délégations DNS et les zones DNS nécessaires pour s’assurer qu’enregistrement DNS inverse l'convertie pour tous les utilisateurs d’internet.

### <a name="will-a-default-reverse-dns-record-be-created-for-my-public-ip-addresses"></a>Un enregistrement DNS inverse par défaut va être créé pour mes adresses IP publiques ?
N° DNS inversée est une fonctionnalité opt-in. Aucun enregistrement DNS inverse de par défaut n’est créés si vous choisissez de ne pas les configurer.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>Quel est le format pour le nom de domaine pleinement qualifié (FQDN) ?
Noms de domaine complets sont spécifiées dans l’ordre chronologique et doivent se terminer par un point (par exemple, « app1.contoso.com. »).

### <a name="what-happens-if-the-validation-checks-for-the-reverse-dns-ive-specified-fail"></a>Que se passe-t-il si les tests de validation pour le DNS inversée, j’ai spécifié ?
Où les DNS inversée pour les contrôles de validation échoue, l’opération de gestion de service va échouer. Veuillez corriger la valeur DNS inversée comme requis et recommencer.

### <a name="can-i-manage-reverse-dns-for-my-azure-website"></a>Puis-je gérer DNS inversée pour mon site Web d’Azure ?
DNS inverse n’est pas pris en charge pour les sites Web d’Azure. DNS inverse est pris en charge pour Azure Virtual Machines.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-public-ip-address"></a>Puis-je configurer plusieurs enregistrements DNS inverses pour mon adresse IP publique ?
N° Azure prend en charge un seul enregistrement DNS inversé pour chaque adresse IP publique. Toutefois, chaque adresse IP publique peut avoir son propre enregistrement DNS inverse.

### <a name="can-i-configure-reverse-dns-records-for-an-ipv6-public-ip-address"></a>Puis-je configurer des enregistrements DNS inversées pour une adresse IP publique de IPv6 ?
N°  À ce stade, les enregistrements DNS inversées sont pris en charge pour les adresses IPv4 publiques IP uniquement.

### <a name="can-i-configure-a-reverse-dns-record-for-my-public-ip-address-without-having-a-domainnamelabel-specified"></a>Puis-je configurer un enregistrement DNS inverse pour mon adresse IP publique sans avoir un DomainNameLabel spécifié ?
N° Pour tirer parti des enregistrements DNS inverses pour vos adresses IP publiques, vous devez spécifier la propriété DomainNameLabel.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>Puis-je envoyer des courriers électroniques à des domaines externes à partir de mes services Azure Compute ?
N° [Services d’azure Compute ne gèrent pas l’envoi de messages électroniques à des domaines externes](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/).
