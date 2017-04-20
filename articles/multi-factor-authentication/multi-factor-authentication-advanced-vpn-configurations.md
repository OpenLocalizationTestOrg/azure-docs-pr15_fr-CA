<properties
    pageTitle="Scénarios avancés avec authentification à plusieurs facteurs Azure et 3ème VPN tiers"
    description="Cette page fournit des informations sur la configuration de configuration pas à pas pour Azure AMF avec la 3ème partie produits."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban" 
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="advanced-scenarios-with-azure-multi-factor-authentication-and-3rd-party-vpn"></a>Scénarios avancés avec authentification à plusieurs facteurs Azure et 3ème VPN tiers
Authentification à plusieurs facteurs Azure peut servir pour connecter de façon transparente avec une variété de solutions VPN 3ème partie.  Cela inclut l’appareil VPN de Cisco® ASA, appareil Citrix NetScaler SSL VPN et l’appliance Juniper réseaux accès/Pulse Secure se connecter sécurisé SSL VPN sécurisé.

## <a name="cisco-asa-vpn-appliance-and-azure-multi-factor-authentication"></a>Solution matérielle-logicielle ASA VPN de Cisco et d’Azure, plusieurs facteurs d’authentification
Authentification à plusieurs facteurs Azure s’intègre parfaitement avec votre solution VPN de Cisco® ASA pour fournir une sécurité supplémentaire pour les connexions d’accès Cisco AnyConnect® VPN et accès au portail.  Cela peut être effectué à l’aide du protocole LDAP ou du rayon.  Sélectionnez une des opérations suivantes pour télécharger les guides de configuration pas à pas détaillé.

Guide de configuration  | Description
------------- | ------------- |
[Cisco ASA avec la Configuration AMF Anyconnect VPN et Azure pour LDAP](http://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | S’intégrant parfaitement votre solution VPN de Cisco ASA AMF Azure à l’aide de LDAP|
[Cisco ASA avec Anyconnect VPN et Azure AMF la Configuration RADIUS](http://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | S’intégrant parfaitement votre solution VPN de Cisco ASA AMF Azure à l’aide de RADIUS

## <a name="citrix-netscaler-ssl-vpn-and-azure-multi-factor-authentication"></a>Authentification à plusieurs facteurs Citrix NetScaler SSL VPN et Azure
Authentification à plusieurs facteurs Azure s’intègre parfaitement avec votre solution matérielle-logicielle Citrix NetScaler SSL VPN pour fournir une sécurité supplémentaire pour les connexions Citrix NetScaler SSL VPN et accès au portail.  Cela peut être effectué à l’aide du protocole LDAP ou du rayon.  Sélectionnez une des opérations suivantes pour télécharger les guides de configuration pas à pas détaillé.

Guide de configuration  | Description
------------- | ------------- |
[Citrix NetScaler SSL VPN et Azure AMF la Configuration LDAP](http://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | S’intégrant parfaitement votre Citrix NetScaler SSL VPN appliance Azure AMF à l’aide de LDAP|
[Citrix NetScaler SSL VPN et Azure AMF la Configuration RADIUS](http://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | S’intégrant parfaitement votre solution matérielle-logicielle Citrix NetScaler SSL VPN AMF Azure à l’aide de RADIUS

##<a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-multi-factor-authentication"></a>Solution matérielle-logicielle Secure SSL VPN de Juniper/Pulse et Azure, plusieurs facteurs d’authentification
Authentification à plusieurs facteurs Azure s’intègre parfaitement avec votre solution matérielle-logicielle sécurisée SSL VPN de Juniper/Pulse pour fournir une sécurité supplémentaire pour les connexions sécurisées SSL VPN de Juniper/Pulse et accès au portail.  Cela peut être effectué à l’aide du protocole LDAP ou du rayon.  Sélectionnez une des opérations suivantes pour télécharger les guides de configuration pas à pas détaillé.

Guide de configuration  | Description
------------- | ------------- |
[Configuration d’AMF sécurisé SSL VPN de Juniper/Pulse et Azure pour LDAP](http://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx)| S’intégrant parfaitement votre Juniper/Pulse sécurisé SSL VPN appliance Azure AMF à l’aide de LDAP|
[Sécurisé SSL VPN de Juniper/Pulse et Azure AMF la Configuration RADIUS](http://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | S’intégrant parfaitement votre solution matérielle-logicielle Secure SSL VPN de Juniper/Pulse AMF Azure à l’aide de RADIUS
