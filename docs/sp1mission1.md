# Mission 1 - Définir un nouveau plan d'adressage

**Compte rendu rédigé par** : BIDANESSY Coumba  
**Formation** : BTS SIO 1ère année - Option SISR  
**Établissement** : Lycée Paul-Louis Courier, Tours  
**Date** : 15 janvier 2026

## 1. Tableau d'adressage

Voici la représentation de mes différents sous-réseau dans le réseau 172.16.40.0/16

| Services       | Nombre d'hôte | Réseaux         | Adresse de diffusion | Passerelle   |
| -------------- | ------------- | --------------- | -------------------- | ------------ |
| Administration | 64            | 172.40.1.128/25 | 172.40.1.255         | 172.40.1.254 |
| Autres         | 122           | 172.40.1.0/25   | 172.40.1.127         | 172.40.1.126 |
| Production     | 134           | 172.40.0.0/24   | 172.40.0.255         | 172.40.0.254 |
| Logistique     | 38            | 172.40.2.64/26  | 172.40.2.127         | 172.40.2.126 |
| VentesEtudes   | 59            | 172.40.2.0 /26  | 172.40.2.63          | 172.40.2.62  |
| Serveurs       |               | 172.16.56.0/24  |  172.16.56.253/24                    |              |


## 2. Explication des calculs VLSM

Pour diviser mon réseau en plusieurs sous-réseaux je dois :

- Calculer d’abord le service qui comporte le plus de périphériques informatiques

Dans ma situation, c'est le service production avec 121 équipements qui est le plus grand service, augmentée d’une marge de 20%, donc à 134 équipements.

En prenant en compte mon réseau 172.16.40.0/16 et le tableau des puissances de 2 pour le calcul de bits, je retrouve le masque adaptée :

- 32 bits au total – la puissance de 2 adapté au nombres d’hôtes  en prenant en compte l’adresse de réseau et de diffusion (broadcast) :

Donc  32- 8 = 24 donc on obtient /24 en masque de sous réseau

*     L’adresse réseau de départ sera : 172.40.0.0/24 
*     L’adresse de diffusion (broadcast) sera : 172.40.0.255
*     L’adresse de passerelle sera : 172.40.0.254
