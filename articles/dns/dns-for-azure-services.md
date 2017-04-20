<properties
  pageTitle="À l’aide d’Azure DNS avec d’autres services Azure | Microsoft Azure"
  description="Comprendre comment utiliser Azure DNS pour résoudre le nom pour d’autres services Azure"
  services="dns"
  documentationCenter="na"
  authors="sdwheeler"
  manager="carmonm"
  editor=""
  tags="azure dns"
/>
<tags
  ms.service="dns"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="infrastructure-services"
  ms.date="09/21/2016"
  ms.author="sewhee"
/>

# <a name="using-azure-dns-with-other-azure-services"></a>À l’aide d’Azure DNS avec d’autres services Azure

Azure DNS est un service hébergé de DNS management et le nom de résolution. Cela vous permet de créer des noms DNS publics pour les autres applications et services que vous avez déployé dans Azure. Créer un nom pour un service Azure dans votre domaine personnalisé est aussi simple que l’ajout d’un enregistrement du type correct pour votre service.

* Pour les adresses IP allouées dynamiquement, vous devez créer un enregistrement DNS CNAME qui correspond au nom DNS que Azure créé pour votre service. Les normes DNS vous empêchent d’utiliser un enregistrement CNAME pour le sommet de la zone.
* Pour les adresses IP statiques, vous pouvez créer un enregistrement DNS A l’aide de n’importe quel nom, y compris un nom de _domaine de type naked_ au sommet de la zone.

Le tableau suivant présente les types d’enregistrements pris en charge qui peuvent être utilisés pour différents services Azure. Comme vous pouvez le voir dans cette table, Azure DNS prend uniquement en charge les enregistrements DNS pour les ressources réseau exposées à Internet. Azure DNS ne peut pas être utilisé pour la résolution des noms d’adresses internes, privées.

| Service Azure | Interface réseau | Description |
|---------------|-------------------|-------------|
| Passerelle d’application | IP publique frontale | Vous pouvez créer un enregistrement DNS de type A ou CNAME. |
| L’équilibreur de charge | IP publique frontale | Vous pouvez créer un enregistrement DNS de type A ou CNAME. Équilibreur de charge peut avoir une adresse IP publique de IPv6 qui est affectée dynamiquement. Par conséquent, vous devez créer un enregistrement CNAME pour une adresse IPv6. |
| Traffic Manager | Nom public | Vous ne pouvez créer un enregistrement CNAME qui mappe le nom de trafficmanager.net affecté à votre profil de gestionnaire de trafic. Pour plus d’informations, consultez [comment Traffic Manager fonctionne](../traffic-manager/traffic-manager-how-traffic-manager-works.md#traffic-manager-example). |
| Service de cloud | Adresse IP publique | Pour les adresses IP statiques, vous pouvez créer un enregistrement DNS A. Pour les adresses IP allouées dynamiquement, vous devez créer un enregistrement CNAME qui mappe le nom _cloudapp.net_ . Cette règle s’applique aux ordinateurs virtuels créés dans le portail classique dans la mesure où elles sont déployées sous la forme d’un service en nuage. Pour plus d’informations, consultez [configurer un nom de domaine personnalisé dans les Services en nuage](../cloud-services/cloud-services-custom-domain-name-portal.md). |
| Service d’application | Adresse IP externe | Pour les adresses IP externes, vous pouvez créer un enregistrement DNS A. Dans le cas contraire, vous devez créer un enregistrement CNAME qui mappe le nom azurewebsites.net. Pour plus d’informations, consultez [mapper un nom de domaine personnalisé pour une application Azure](../app-service-web/web-sites-custom-domain-name.md) |
| Ordinateurs virtuels de gestionnaire de ressources | Adresse IP publique | Ordinateurs virtuels de gestionnaire de ressources peut avoir des adresses IP publiques. Un ordinateur virtuel avec une adresse IP publique peut également être derrière un équilibreur de charge. Vous pouvez créer un enregistrement DNS de type A ou CNAME pour l’adresse publique. Ce nom personnalisé peut être utilisé pour ignorer l’adresse VIP de l’équilibreur de charge. |
| Ordinateurs virtuels classiques | Adresse IP publique | Ordinateurs virtuels classiques créé à l’aide de PowerShell ou CLI peut être configuré avec une adresse virtuelle (réservée) dynamique ou statique. Vous pouvez créer un CNAME DNS ou un enregistrement, respectivement. |
