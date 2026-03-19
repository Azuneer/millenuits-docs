# SP2 - Mission 2 - Mise en place du serveur DHCP sous Linux

> 👤 **Fiche rédigée par** : GADONNAUD Ewen  
> 🎓 **Formation** : BTS SIO 1ère année - Option SISR  
> 🏫 **Établissement** : Lycée Paul-Louis Courier, Tours  
> 📅 **Date** : Mars 2026

Ce guide couvre l'installation et la configuration de **Kea DHCP** sur le serveur MN11 (Debian), afin de distribuer des adresses IP à plusieurs VLANs via un mécanisme de relay DHCP (`ip helper-address`) configuré sur le routeur Cisco 1921.

---

## Prérequis

- Serveur MN11 sous Debian avec accès root
- Plan d'adressage VLSM défini (voir Mission 1 SP1)
- Routeur Cisco 1921 configuré avec des sous-interfaces par VLAN

---

## 1. Installation de Kea DHCP

`isc-dhcp-server` étant déprécié, on utilise son remplaçant moderne **Kea DHCP**, également développé par l'ISC.

```bash
sudo apt update && sudo apt install kea-dhcp4-server
```

---

## 2. Configuration

La configuration se fait en JSON dans `/etc/kea/kea-dhcp4.conf`. Sauvegarder d'abord le fichier par défaut :

```bash
sudo cp /etc/kea/kea-dhcp4.conf /etc/kea/kea-dhcp4.conf.bak
```

Remplacer ensuite le contenu par la configuration suivante :

```json
{
  "Dhcp4": {
    "interfaces-config": {
      "interfaces": ["ens3"]
    },
    "lease-database": {
      "type": "memfile",
      "persist": true,
      "name": "/var/lib/kea/dhcp4.leases"
    },
    "option-data": [ { "name": "domain-name", "data": "millenuits.fr" } ],
    "subnet4": [
      {
        "id": 1,
        "subnet": "172.40.0.0/24",
        "pools": [{ "pool": "172.40.0.2 - 172.40.0.253" }],
        "option-data": [
          { "name": "routers", "data": "172.40.0.254" },
          { "name": "domain-name-servers", "data": "8.8.8.8" }
        ]
      },
      {
        "id": 2,
        "subnet": "172.40.1.0/25",
        "pools": [{ "pool": "172.40.1.2 - 172.40.1.125" }],
        "option-data": [
          { "name": "routers", "data": "172.40.1.126" },
          { "name": "domain-name-servers", "data": "8.8.8.8" }
        ]
      },
      {
        "id": 3,
        "subnet": "172.40.1.128/25",
        "pools": [{ "pool": "172.40.1.130 - 172.40.1.253" }],
        "option-data": [
          { "name": "routers", "data": "172.40.1.254" },
          { "name": "domain-name-servers", "data": "8.8.8.8" }
        ]
      },
      {
        "id": 4,
        "subnet": "172.40.2.0/26",
        "pools": [{ "pool": "172.40.2.2 - 172.40.2.61" }],
        "option-data": [
          { "name": "routers", "data": "172.40.2.62" },
          { "name": "domain-name-servers", "data": "8.8.8.8" }
        ]
      },
      {
        "id": 5,
        "subnet": "172.40.2.64/26",
        "pools": [{ "pool": "172.40.2.66 - 172.40.2.125" }],
        "option-data": [
          { "name": "routers", "data": "172.40.2.126" },
          { "name": "domain-name-servers", "data": "8.8.8.8" }
        ]
      }
    ]
  }
}
```

> **Note** : Adapter `"interfaces"` au nom réel de l'interface réseau du serveur (`ip a` pour vérifier). Dans notre cas, c'est l'interface `ens3`

> **Note** : Depuis Kea 2.6.0, chaque subnet doit avoir un champ `"id"` explicite et unique. Sans ce champ, Kea refuse de démarrer avec l'erreur `subnet configuration failed: missing parameter`.

---

## 3. Création du fichier de leases

Kea ne crée pas automatiquement son fichier de leases (baux). Il faut le créer manuellement et lui attribuer les bonnes permissions :

```bash
sudo mkdir -p /var/lib/kea
sudo touch /var/lib/kea/dhcp4.leases
sudo chown -R _kea:_kea /var/lib/kea
sudo chmod 755 /var/lib/kea
sudo chmod 644 /var/lib/kea/dhcp4.leases
```

---

## 4. Problème AppArmor

Sur Debian, **AppArmor est actif par défaut** et son profil pour Kea l'empêche d'accéder à ses propres fichiers de configuration et de leases, même avec les bonnes permissions filesystem. Cela se manifeste par l'erreur :

```
Unable to open database: unable to open '/var/lib/kea/dhcp4.leases'
```

Pour corriger, désactiver le profil AppArmor de Kea :

```bash
sudo ln -s /etc/apparmor.d/usr.sbin.kea-dhcp4 /etc/apparmor.d/disable/
sudo apparmor_parser -R /etc/apparmor.d/usr.sbin.kea-dhcp4
```

---

## 5. Démarrage du service

```bash
sudo systemctl enable kea-dhcp4-server
sudo systemctl restart kea-dhcp4-server
sudo systemctl status kea-dhcp4-server
```

Pour surveiller les leases attribuées :

```bash
cat /var/lib/kea/dhcp4.leases
```

---

## 6. Configuration du relay DHCP côté routeur Cisco

Pour que les clients dans chaque VLAN puissent contacter le serveur DHCP centralisé, configurer `ip helper-address` sur chaque sous-interface VLAN du routeur 1921 :

```
interface fa0/X.VLAN_ID
 ip helper-address 172.16.55.11
```

À répéter sur toutes les sous-interfaces. Le routeur intercepte les broadcasts DHCP des clients et les relaie en unicast vers MN11 en ajoutant le champ `giaddr` (IP de la sous-interface), ce qui permet à Kea d'identifier le bon subnet et d'attribuer une adresse du bon pool.

---

## Récapitulatif des subnets

| ID  | Subnet            | Pool            | Passerelle     | VLAN           |
| --- | ----------------- | --------------- | -------------- | -------------- |
| 1   | `172.40.0.0/24`   | `.2` → `.253`   | `172.40.0.254` | Production     |
| 2   | `172.40.1.0/25`   | `.2` → `.125`   | `172.40.1.126` | Autres         |
| 3   | `172.40.1.128/25` | `.130` → `.253` | `172.40.1.254` | Administration |
| 4   | `172.40.2.0/26`   | `.2` → `.61`    | `172.40.2.62`  | VentesEtudes   |
| 5   | `172.40.2.64/26`  | `.66` → `.125`  | `172.40.2.126` | 
