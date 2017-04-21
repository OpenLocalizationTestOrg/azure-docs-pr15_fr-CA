##<a name="tcp-settings-for-azure-vms"></a>Paramètres TCP pour les machines virtuelles d’Azure

Azure VM communique avec l’Internet public à l’aide de [NAT] [ nat] (Network Address Translation). Périphériques NAT attribuer une adresse IP publique et un port à une machine virtuelle Azure, ce qui qui VM pour établir un socket pour la communication avec d’autres périphériques. Si les paquets interruption du flux par le biais de cet socket après une heure donnée, le périphérique NAT supprime le mappage et le socket est disponible être utilisé par d’autres machines virtuelles.

Il s’agit d’un comportement NAT commun, ce qui peut provoquer des problèmes de communication sur les applications TCP basé qui attendent un socket d’être conservées au-delà d’un délai d’attente. Il existe deux paramètres de temporisation d’inactivité à prendre en compte pour les sessions dans un état *établi de connexion* :

- **entrant** par l’intermédiaire de l' [équilibreur de charge Azure][azure-lb-timeout]. Ce délai d’attente par défaut est de 4 minutes et peut être ajustée jusqu'à 30 minutes.
- **sortant** à l’aide de [SNAT] [ snat] (NAT Source). Ce délai est défini à 4 minutes et ne peut pas être ajusté.

Pour garantir que les connexions ne sont pas perdues au-delà de la limite de délai d’attente, il se peut que vous devez vous assurer que votre application conserve la session active, ou vous pouvez configurer le système d’exploitation sous-jacent pour cela. Les paramètres à utiliser sont différents pour les systèmes Linux et Windows, comme illustré ci-dessous.

Pour [Linux][linux], vous devez modifier les variables de noyau ci-dessous.
NET.IPv4.tcp_keepalive_time = 120 net.ipv4.tcp_keepalive_intvl = 30 net.ipv4.tcp_keepalive_probes = 8
 
Pour [Windows][windows], vous devez modifier les valeurs de Registre ci-dessous.
KeepAliveInterval = 30 KeepAliveTime = 120 TcpMaxDataRetransmissions = 8


Les paramètres ci-dessus garantissent un paquet d’activité est envoyé après 2 minutes (120 secondes) d’inactivité et ensuite envoyé toutes les 30 secondes. Et si vous ne parvenez pas 8 de ces paquets, la session est supprimée.

<!-- links -->
[nat]: http://computer.howstuffworks.com/nat.htm
[snat]: ../load-balancer/load-balancer-overview.md/#source-nat
[linux]: http://tldp.org/HOWTO/TCP-Keepalive-HOWTO/usingkeepalive.html
[windows]: http://blogs.technet.com/b/nettracer/archive/2010/06/03/things-that-you-may-want-to-know-about-tcp-keepalives.aspx
[azure-lb-timeout]: ../load-balancer/load-balancer-tcp-idle-timeout.md