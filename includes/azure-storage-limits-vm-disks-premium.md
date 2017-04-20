**Disques de l’ordinateur virtuel : par des limites de compte**

Ressources|Limite par défaut
---|---
Capacité totale du disque par compte|35 TO
Capacité totale de capture instantanée par compte|10 TO
La bande passante maximale par compte (ingress + de sortie<sup>1</sup>)|< = Gbits/50 s

<sup>1</sup> *Entrée* fait référence à toutes les données (requêtes) envoyées à un compte de stockage. *Sortie* fait référence à toutes les données (réponses) à partir d’un compte de stockage en cours de réception.

**Disques de l’ordinateur virtuel : par des limites de disque**

Type de disque de stockage de prime | P10 | P20 | P30
---|---|---|---
Taille du disque | Talon 128 | Talon 512 | Talon de 1024 (1 To)
Max IOPS par disque | 500 | 2300 | 5000
Débit max par disque | 100 Mo par seconde | 150 Mo par seconde | 200 Mo par seconde
Nombre maximum de disques par le compte de stockage | 280 | 70 | 35

**Disques de l’ordinateur virtuel : par les limites de la machine virtuelle**

Ressources|Limite par défaut
---|---
E/s de max par machine virtuelle|80 000 IOPS avec GS5 MV<sup>1</sup>
Débit max par machine virtuelle|2 000 Mo/s avec GS5 MV<sup>1</sup>

<sup>1</sup> [Taille de mémoire virtuelle](../articles/virtual-machines/virtual-machines-linux-sizes.md) pour voir les limites sur les autres formats de machine virtuelle. 
