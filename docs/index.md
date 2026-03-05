# Contexte de l'entreprise Mille Nuits

## 1. Secteur d'activité

La production textile est majoritairement délocalisée en Asie, mais la fabrication de couettes, oreillers et couvertures reste en partie européenne en raison :
- d'un besoin limité en main-d'œuvre,
- du coût élevé du transport des matières premières volumineuses (ouate, plumes).

Le secteur intègre de fortes exigences écologiques et sanitaires :
- respect de l'environnement,
- confort et entretien des produits,
- santé des consommateurs (antiacariens, anti-allergènes),
- traçabilité et origine des fibres naturelles (plumes, duvets).

**Principaux acteurs du marché français :** Dodo, Abeil, Lestra, Plumka, Tempur, Pyrénex.

---

## 2. Présentation de l'entreprise Mille Nuits

| Caractéristique | Détail |
|---|---|
| Statut | SA au capital de 4 500 000 € |
| Positionnement | Leader français des couettes et oreillers |
| Production | Plus de 35 000 articles par jour |

**Clients :**
- distributeurs (marque distributeur),
- hôtellerie,
- collectivités (notamment hôpitaux),
- vente par correspondance (La Redoute, 3 Suisses),
- particuliers.

### Implantation

**Site historique (Baugé-en-Anjou) :**
- production,
- administration,
- siège social (locaux en propriété).

**Site logistique (Joué-lès-Tours) :**
- stockage et expédition,
- parfois stockage de matières premières,
- locaux loués.

> Distance entre les sites : 90 km, liaison autoroutière proche.

### Effectif

**167 salariés** (+ intérimaires ponctuels)

| Service | Effectif |
|---|---|
| Production | 81 |
| Logistique | 36 |
| Ventes | 24 |
| Informatique | 3 |
| Autres (qualité, BE, achats, RH, direction…) | — |

---

## 3. Système informatique

### 3.1 Infrastructures

- Serveur réseau (Windows Server) : AD, DNS, DHCP, fichiers
- Serveur de messagerie
- Serveur PGI (Open ERP) : ventes, achats, stocks, comptabilité
- Site Internet hébergé à l'extérieur
- Antivirus installé sur chaque poste
- Gestion du parc via tableur
- Réseau de production automatisé isolé et géré par un prestataire externe

### 3.2 Gestion du SI

**Équipe interne :**
- 1 technicien système/réseau & informatique industrielle,
- 1 technicien système & développement,
- 1 DSI.

**Prestataire externe :**
- maintenance du PGI,
- développements majeurs.

> Le réseau est principalement géré en interne.

### 3.3 Équipements utilisateurs

- Postes fixes Windows pour la majorité du personnel
- Ordinateurs portables Windows pour : commerciaux, direction, responsables
- Pas de Wi-Fi : connexions Ethernet uniquement
- Accès informatique limité en production et logistique
- Messagerie nominative : `prenom.nom@millenuits.com`

### Serveurs (site historique)

| Serveur | Rôles |
|---|---|
| MN01 | AD, DNS, DHCP, fichiers |
| MN02 | Messagerie |
| MN03 | PGI (ventes, achats, stocks, comptabilité) |
