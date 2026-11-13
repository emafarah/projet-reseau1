# 🌐 Projet Réseau d'Entreprise – Segmentation VLAN, Routage & Services (Cisco Packet Tracer)

> Conception et configuration d'un réseau d'entreprise segmenté en VLAN, avec routage inter-VLAN, serveurs DNS/DHCP et accès à Internet simulé.
> Projet réalisé en formation **Les Codeuses** – [ajouter la date / la promo].

![Topologie du réseau](./images/topologie.png)

---

## 🎯 Objectif du projet

Concevoir un réseau d'entreprise **sécurisé et organisé** qui :

- sépare les flux par service grâce aux **VLAN** (clients, gestion IT, serveurs) ;
- distribue automatiquement les adresses IP via un **serveur DHCP** ;
- résout les noms de domaine via un **serveur DNS** ;
- permet la communication entre VLAN via un **routeur** (routage inter-VLAN) ;
- donne accès à un réseau externe simulé (**Internet / Google 8.8.8.8**) à travers deux routeurs.

---

## 🗺️ Architecture

### Plan d'adressage

| Zone | Réseau | Masque | Hôtes utilisables | Ports switch |
|------|--------|--------|-------------------|--------------|
| **VLAN 10 – Clients** | 192.168.10.0 | /24 | 254 | Fa0/1 – Fa0/8 |
| **VLAN 20 – Gestion IT** | 192.168.20.0 | /27 | 30 | Fa0/9 – Fa0/16 |
| **VLAN 30 – Serveurs** | 192.168.30.0 | /28 | 14 | Fa0/17 – Fa0/24 |
| **Liaison WAN (Router0 ↔ Router1)** | 94.100.0.0 | /30 | 2 | Gig0/0 |
| **Réseau « Internet »** | 8.0.0.0 | /8 | – | Router1 Gig0/1 |
| **Serveur Google (simulé)** | 8.8.8.8 | /8 | – | Switch1 Fa0/1 |

> 💡 **Choix de dimensionnement** : chaque sous-réseau est adapté à son besoin réel (VLSM) : un /24 pour de nombreux postes clients, un /27 pour l'équipe IT, un /28 pour une poignée de serveurs et un /30 pour une liaison point à point.

### Équipements

- 2 routeurs (Router0 : routeur d'accès/LAN, Router1 : routeur « opérateur »)
- 2 switchs (1 switch LAN d'entreprise, 1 switch côté « Internet »)
- 4 PC (PC0, PC1 → VLAN 10 ; PC2, PC3 → VLAN 20)
- 2 serveurs internes (DNS, DHCP → VLAN 30)
- 1 serveur « Google » simulant Internet

---

## ⚙️ Ce qui a été mis en place

- [x] Création et nommage des **VLAN 10, 20 et 30**
- [x] Affectation des ports en mode **access** par VLAN
- [x] Liaison **trunk 802.1Q** entre le switch et Router0 (Gig0/1)
- [x] **Routage inter-VLAN** [router-on-a-stick avec sous-interfaces – à confirmer]
- [x] **Plan d'adressage VLSM** (/24, /27, /28, /30)
- [x] Configuration d'un **serveur DHCP** centralisé [avec `ip helper-address` pour les VLAN 10 et 20 – à confirmer]
- [x] Configuration d'un **serveur DNS**
- [x] **Routage statique** entre Router0 et Router1 [+ route par défaut – à confirmer]
- [x] [NAT/PAT pour l'accès à Internet – à confirmer ou supprimer]
- [x] Tests de connectivité (ping, traceroute) de bout en bout

---

## 🔧 Extraits de configuration

> Remplace ces exemples par tes vraies configurations.

<details>
<summary><b>Switch – VLAN et ports</b></summary>

```
vlan 10
 name Clients
vlan 20
 name Gestion_IT
vlan 30
 name Serveurs

interface range fa0/1 - 8
 switchport mode access
 switchport access vlan 10

interface range fa0/9 - 16
 switchport mode access
 switchport access vlan 20

interface range fa0/17 - 24
 switchport mode access
 switchport access vlan 30

interface gig0/1
 switchport mode trunk
```
</details>

<details>
<summary><b>Router0 – Routage inter-VLAN</b></summary>

```
interface gig0/1.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0
 ip helper-address 192.168.30.[X]

interface gig0/1.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.224
 ip helper-address 192.168.30.[X]

interface gig0/1.30
 encapsulation dot1Q 30
 ip address 192.168.30.1 255.255.255.240
```
</details>

<details>
<summary><b>Router0 – Liaison WAN et route par défaut</b></summary>

```
interface gig0/0
 ip address 94.100.0.1 255.255.255.252
 no shutdown

ip route 0.0.0.0 0.0.0.0 94.100.0.2
```
</details>

---

## ✅ Tests et validation

| Test | Résultat attendu | Statut |
|------|------------------|--------|
| PC0 obtient une IP automatiquement (DHCP) | Adresse dans 192.168.10.0/24 | ✅ |
| PC2 obtient une IP automatiquement (DHCP) | Adresse dans 192.168.20.0/27 | ✅ |
| Ping PC0 → PC2 (inter-VLAN) | Réponse | ✅ |
| Ping PC0 → Serveur DNS | Réponse | ✅ |
| Ping PC0 → 8.8.8.8 | Réponse | ✅ |
| Résolution DNS | Nom → IP | ✅ |

> 📸 Ajoute ici des captures d'écran de tes tests dans `./images/`.

---

## 🧠 Compétences démontrées

**Réseau**
- Segmentation en VLAN et configuration de ports access/trunk
- Routage inter-VLAN
- Adressage IPv4 et découpage en sous-réseaux (VLSM)
- Routage statique
- Configuration de routeurs et switchs Cisco (IOS, CLI)

**Services**
- Déploiement DHCP et DNS
- Relais DHCP entre VLAN

**Méthode**
- Lecture et conception d'une topologie réseau
- Documentation technique
- Tests et dépannage (ping, traceroute, vérification des tables de routage)

---

## 📂 Structure du dépôt

```
.
├── README.md
├── projet-reseau.pkt        # Fichier Packet Tracer
├── configs/
│   ├── router0.txt
│   ├── router1.txt
│   ├── switch0.txt
│   └── switch1.txt
└── images/
    └── topologie.png
```

## ▶️ Utilisation

1. Installer [Cisco Packet Tracer](https://www.netacad.com/courses/packet-tracer)
2. Cloner le dépôt : `git clone https://github.com/[ton-pseudo]/[nom-du-repo].git`
3. Ouvrir `projet-reseau.pkt` dans Packet Tracer
4. Lancer des pings depuis les PC pour valider la connectivité

---

## 🚀 Pistes d'amélioration

- Ajouter des **ACL** pour restreindre l'accès au VLAN Gestion IT
- Mettre en place la **sécurité des ports** (port-security) et désactiver les ports inutilisés
- Ajouter la redondance (EtherChannel, deuxième routeur / HSRP)
- Passer à un routage dynamique (OSPF)
- Configurer SSH pour l'administration à distance

---

## 👩‍💻 Auteure

**[Ton prénom / nom]** – [Formation Les Codeuses – Administration réseau / Développement web]
🔗 [LinkedIn](https://linkedin.com/in/...) · 📧 [email]
