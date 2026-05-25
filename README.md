# Guide d'installation et configuration de Sophos Firewall sous VMware

**Auteure :** Alexandra ASSAGA

**Date :** 25 Mai 2026

> **Environnement :** VMware Workstation | **Version Sophos :** SFOS 22.0.0 GA-Build411 | **Modèle :** SF01V

---

## Sommaire

0. [DAT(Document d'Architecture Technique)](#0-DAT)
1. [Prérequis](#1-prérequis)
2. [Configuration réseau VMware](#2-configuration-réseau-vmware)
3. [Création de la VM Sophos](#3-création-de-la-vm-sophos)
4. [Premier démarrage et configuration console](#4-premier-démarrage-et-configuration-console)
5. [Accès à l'interface Web](#5-accès-à-linterface-web)
6. [Enregistrement et licence](#6-enregistrement-et-licence)
7. [Configuration initiale (assistant)](#7-configuration-initiale-assistant)

---

## 0. DAT (Document d'Architecture Technique)

```
PC Hôte Windows
├── Adaptateur VMnet1 : 172.16.16.1/24  ──────────┐
├── Adaptateur VMnet8 : 192.168.129.x/24           │
└── Connexion Internet (physique)                   │
                                                    │
VM Sophos Firewall (SF01V)                          │
├── PortA (LAN) ← Carte réseau 2 (VMnet1) : 172.16.16.16/24 ◄─┘
└── PortB (WAN) ← Carte réseau 1 (VMnet8/NAT) : DHCP

VM Windows 10 Client
└── Carte réseau (VMnet1) : 172.16.16.x/24
    └── Passerelle : 172.16.16.16 (Sophos LAN)
```

---

## 1. Prérequis

### Matériel recommandé
| Composant | Minimum | Recommandé |
|-----------|---------|-----------|
| RAM allouée à la VM | 2 Go | **4 Go** |
| Disque dur | 32 Go (OS) + 80 Go (logs) | 32 Go + 80 Go |
| CPU | 1 vCPU | 2 vCPU |

### Logiciels nécessaires
- **VMware Workstation Pro** (ou Player) / VirtualBox / Hyper-V / KVM / Citrix Hypervisor
- **Image ISO ou l'appliance Sophos Firewall** (SF01V) — téléchargeable sur [Sophos](https://www.sophos.com/fr-fr/support/downloads)
![image](https://hackmd.io/_uploads/S1zvqiZxfx.png)
![image](https://hackmd.io/_uploads/rkG55s-eGe.png)
![image](https://hackmd.io/_uploads/SkKscsWgMg.png)
![image](https://hackmd.io/_uploads/r1OZojWxfg.png)
![image](https://hackmd.io/_uploads/BJIzjobxfl.png)

- Un compte **Sophos Central** (gratuit) pour l'enregistrement
![image](https://hackmd.io/_uploads/SkwujibxGe.png)

---

## 2. Configuration réseau VMware

Avant de créer la VM, configurer les réseaux virtuels VMware.

### Ouvrir l'éditeur de réseau virtuel
```
VMware → Édition → Éditeur de réseau virtuel
```

### Configuration des interfaces

| VMnet | Type | Réseau | Usage |
|-------|------|--------|-------|
| **VMnet1** | Hôte uniquement | `172.16.0.0/24` | LAN (interface admin) |
| **VMnet8** | NAT | `192.168.129.0/24` | WAN (accès internet) |

### Paramètres VMnet1
- Connecter un hôte et un adaptateur virtuel (Dans mon cas j'ai utilisé ma vm windows 10 comme machine cliente)
![image](https://hackmd.io/_uploads/H1TEnj-xGl.png)

- Désactiver le service DHCP local car Sophos gère le DHCP
- **Adresse IP de sous-réseau :** `172.16.0.0`
- **Masque :** `255.255.255.0`

![image](https://hackmd.io/_uploads/BJbCji-xGe.png)

> ⚠️ **Important :** L'adaptateur VMware VMnet1 sur le PC hôte recevra automatiquement l'IP `172.16.0.1`

![image](https://hackmd.io/_uploads/SkFgnoWgMl.png)

---

## 3. Création de la VM Sophos

Dans mon lab de test, j'ai directement téléchargé l'appliance (machine virtuelle préconfiguré) du firewall sophos que je vais importer dans vmware, 

![image](https://hackmd.io/_uploads/Sk5z6sZxMx.png)
![image](https://hackmd.io/_uploads/rJgiaoZgGx.png)
![image](https://hackmd.io/_uploads/BJl8ipjWxGg.png)
![image](https://hackmd.io/_uploads/B1RiaiWxGx.png)
![image](https://hackmd.io/_uploads/SyO36sZeGx.png)
![image](https://hackmd.io/_uploads/B10n6jZlfl.png)
![image](https://hackmd.io/_uploads/BkgKTpsWeMg.png)
![image](https://hackmd.io/_uploads/HyxR6ibxMx.png)

### Paramètres de la machine virtuelle

| Périphérique | Configuration |
|-------------|--------------|
| Mémoire | **4096 Mo** |
| Processeurs | 1 |
| Disque dur 1 (SCSI) | 32 Go |
| Disque dur 2 (SCSI) | 80 Go |
| **Carte réseau 1** | **Personnalisé : VMnet1 (Hôte)** |
| **Carte réseau 2** | **Personnalisé : VMnet8 (NAT)** |
| **Carte réseau 3** | Pont |

![image](https://hackmd.io/_uploads/ryVq3s-ezl.png)

> **Correspondance des interfaces Sophos :**
> - **PortA (LAN)** = Carte réseau 2 → VMnet1
> - **PortB (WAN)** = Carte réseau 1 → VMnet8 (NAT)

---

## 4. Premier démarrage et configuration console

### Menu principal Sophos (console noire)

```
AA. Device Activation
 1. Network Configuration
 2. System Configuration
 3. Route Configuration
 4. Device Console
 5. Device Management
 6. VPN Management
 7. Shutdown/Reboot Device
 0. Exit
```

![image](https://hackmd.io/_uploads/HJieAoZlMx.png)
![image](https://hackmd.io/_uploads/BkzW0sZxfl.png)
![image](https://hackmd.io/_uploads/SJtWRjbeze.png)

> Le mot de passe par defaut c'est **admin**, attention le clavier par defaut est le **qwerty**

![image](https://hackmd.io/_uploads/BJcBCibefl.png)
![image](https://hackmd.io/_uploads/rJgPCo-lzg.png)

### Vérifier la configuration réseau (option 1)

**PortA (LAN) :**
```
Interface Name  : PortA (Physical)
Zone Name       : LAN
IPv4/Netmask    : 172.16.0.254/255.255.255.0 (Static)
IPV4 Gateway    : N.A.
```
![image](https://hackmd.io/_uploads/HJAxy3Zgzg.png)
![image](https://hackmd.io/_uploads/BkbR0o-gze.png)

**PortB (WAN) :**
```
Interface Name  : PortB (Physical)
Zone Name       : WAN
IPv4/Netmask    : x.x.x.x (DHCP)
IPV4 Gateway    : x.x.x.x
```
![image](https://hackmd.io/_uploads/S1220ibeGg.png)

### Configuration DNS (vérification)
```
Current IPv4 DNS Configuration : Obtain from DHCP
DNS 1 : x.x.x.x
DNS 2 : x.x.x.x
DNS 3 : x.x.x.x
```
![image](https://hackmd.io/_uploads/rkcb1hblfg.png)

---

## 5. Accès à l'interface Web

### Configuration IP sur ma machine cliente windows 10:

Sur la carte **VMware Network Adapter VMnet1** du PC hôte, configurer une IP statique :

| Paramètre | Valeur |
|-----------|--------|
| Adresse IP | `172.16.0.3` |
| Masque | `255.255.255.0` |
| Passerelle | `172.16.0.254` |

![image](https://hackmd.io/_uploads/H1wU1nWeMl.png)

### Vérifier la connectivité

```cmd
ping 172.16.0.254
```
![image](https://hackmd.io/_uploads/HkY512Zeze.png)

Résultat attendu :
```
Réponse de 172.16.0.254 : octets=32 temps<1ms TTL=64
Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%)
```

### Accéder à l'interface web

Ouvrir un navigateur et aller sur :
```
https://172.16.0.254:4444
```
> Accepter l'avertissement de certificat SSL auto-signé.

![image](https://hackmd.io/_uploads/BJ6hk2Zgzl.png)
![image](https://hackmd.io/_uploads/Sk46kh-lfl.png)
![image](https://hackmd.io/_uploads/BJzRJhbxMe.png)
![image](https://hackmd.io/_uploads/r1Om-n-efx.png)
![image](https://hackmd.io/_uploads/S1u4Z2bxze.png)

---

## 6. Enregistrement et licence

### Obtenir un numéro de série (essai 30 jours)
1. Aller sur [Sophos Firewall Home Use](https://www.sophos.com/fr-fr/products/free-tools/sophos-xg-firewall-home-edition)
2. S'inscrire → recevoir le numéro de série par email
3. Exemple de format : `xxxxxxxxxxxxxxx`
![image](https://hackmd.io/_uploads/SJ8l7hZgze.png)

### Enregistrement dans l'assistant
- Sélectionner **"J'ai un numéro de série existant"**
- Saisir le numéro reçu par email
- Cliquer **Continuer**
- Se connecter à **Sophos Central** pour associer le pare-feu
![image](https://hackmd.io/_uploads/SyDZm2Zefx.png)
![image](https://hackmd.io/_uploads/r1d1G2Wlzx.png)
![image](https://hackmd.io/_uploads/S18NGn-xGl.png)
![image](https://hackmd.io/_uploads/SJtDGhWxzx.png)
![image](https://hackmd.io/_uploads/HJG9M3-efl.png)

### Licences incluses (évaluation 30 jours)

| Module | État |
|--------|------|
| Pare-feu de base (VPN, Sans fil) | Évaluation |
| Protection réseau (IPS, X-Ops) | Évaluation |
| Web Protection | Évaluation |
| NDR Essentials for Firewall | Évaluation |
| Protection Zero-day | Évaluation |
| Central Orchestration | Évaluation |
| DNS Protection | Évaluation |

![image](https://hackmd.io/_uploads/Bk5rmhZgze.png)
![image](https://hackmd.io/_uploads/H1VUX3ZxGg.png)

---

## 7. Configuration initiale (assistant)

### Nom et fuseau horaire
- **Nom du pare-feu :** `FW-LAB-01` *(exemple)*
- **Fuseau horaire :** `Europe/Zurich`
- Configuration Réseau (LAN)
- Notifications et sauvegardes
- Récapitulatif de la configuration
![image](https://hackmd.io/_uploads/Hk7BZ2WeMg.png)
![image](https://hackmd.io/_uploads/HyGxV2bgzl.png)
![image](https://hackmd.io/_uploads/r184VnbeMe.png)
![image](https://hackmd.io/_uploads/r1CPV3Zxzl.png)

### Protection des réseaux
Pour un environnement de lab, **ne rien cocher** et cliquer **Continuer** — les politiques peuvent être configurées manuellement ensuite.

> Les options disponibles :
> - Protection contre les menaces réseau (IPS)
> - Protection contre les sites Web suspects
> - Contrôle antimalware
> - Protection Zero-day (Sandbox)

![image](https://hackmd.io/_uploads/ByyWEnZlzl.png)
![image](https://hackmd.io/_uploads/H11t43ZlGe.png)
![image](https://hackmd.io/_uploads/rkTtVhZxGe.png)

> Le mot de passe sera le nouveau mot de passe configuré lors de son installation

![image](https://hackmd.io/_uploads/HJrc43beGl.png)
![image](https://hackmd.io/_uploads/HJdTN3WgMx.png)
![image](https://hackmd.io/_uploads/BkaEBnZeMx.png)
![image](https://hackmd.io/_uploads/BJAeB2Wxzl.png)
![image](https://hackmd.io/_uploads/BJCZBhZlze.png)
![image](https://hackmd.io/_uploads/HJSfr3-efx.png)

### Verifier la configuration du DHCP

J'ai modifié la plage d'adressage du serveur DHCP car l'ip **172.16.0.1** était déjà l'ip par défaut de ma carte vmnet1 sinon il devait avoir conflit d'adressage ip.

![image](https://hackmd.io/_uploads/r1Y3Snbxfx.png)
![image](https://hackmd.io/_uploads/By-pHn-gfg.png)
![image](https://hackmd.io/_uploads/B1xq5r2-lfe.png)
![image](https://hackmd.io/_uploads/S1NjB2bxze.png)
![image](https://hackmd.io/_uploads/BJCsSnWlzx.png)
![image](https://hackmd.io/_uploads/HklASh-ezg.png)
![image](https://hackmd.io/_uploads/BkAGshWxMe.png)

---

## Ressources utiles

- [Documentation Sophos Firewall](https://docs.sophos.com/nsg/sophos-utm/utm/9.7/help/en-us/Content/utm/utmAdminGuide/SupportDocumentation.htm)
- [Téléchargement Sophos Home](https://www.sophos.com/fr-fr/free-tools/sophos-xg-firewall-home-edition/software
- [Sophos Central](https://central.sophos.com)
- [Community Sophos](https://community.sophos.com)

---

*Document réalisée par Alexandra ASSAGA*
