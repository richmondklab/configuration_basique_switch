# TP Cisco – Configuration de base d'un commutateur (SVI, VLAN 99, accès distant)

Travaux pratiques réalisés avec **Cisco Packet Tracer** : câblage d'un petit réseau, configuration de base d'un commutateur Cisco, puis vérification de la connectivité et de la gestion à distance.

## Objectifs

1. Câbler le réseau et vérifier la configuration par défaut du commutateur
2. Configurer les paramètres de base des périphériques réseau
3. Vérifier et tester la connectivité réseau

## Table d'adressage

![Table d'adressage](https://github.com/richmondklab/configuration_basique_switch/blob/main/table%20d'adressage.png?raw=true)

---

## Partie 1 : Câblage et configuration par défaut

### Étape 1 – Câblage

![Câblage](https://github.com/richmondklab/configuration_basique_switch/blob/main/cablage.png?raw=true)

**Console vs Telnet/SSH**

Un commutateur neuf n'a aucune configuration. La console est un accès physique direct qui fonctionne sans adresse IP, sans mot de passe et sans réseau : c'est le seul moyen de faire la configuration initiale. Telnet et SSH passent par le réseau et exigent une adresse IP, des mots de passe vty (et, pour SSH, un nom de domaine, des clés RSA et des utilisateurs), qui n'existent pas encore.

### Étape 2 – Configuration par défaut

**`show running-config`**

![running-config](captures/p1-e2-b-running-config.png)

- Interfaces GigabitEthernet : Gi1/0/1 à Gi1/0/24 et Gi1/1/1 à Gi1/1/4, soit **28** au total.
- Lignes vty : `0 4` et `5 15`.

**`show startup-config`**

![startup-config](captures/p1-e2-c-startup-config.png)

- Aucune configuration n'a été enregistrée en NVRAM : elle n'existe qu'en RAM (running-config).

**`show interface vlan1`**

![show interface vlan1](captures/p1-e2-d-interface-vlan1.png)

- Adresse IP : aucune pour le moment.
- Adresse MAC de la SVI : `0060.2fde.172d`
- Interface opérationnelle : non.

**`show ip interface vlan1`**

![show ip interface vlan1](captures/p1-e2-e-ip-interface-vlan1.png)

```text
Vlan1 is administratively down, line protocol is down
  Internet protocol processing disabled
```

**Après branchement du câble Ethernet**

![ip interface vlan1 après branchement](captures/p1-e2-f-ip-interface-vlan1.png)

- Aucun changement : la SVI reste « administratively down » tant qu'elle n'est pas activée.

**Activation de la SVI VLAN 1**

![activation vlan1](captures/p1-e2-g-no-shutdown.png)

**`show ip interface vlan1` après activation**

![ip interface vlan1 activé](captures/p1-e2-h-ip-interface-vlan1.png)

- `Vlan1 is up, line protocol is up` — « Internet protocol processing disabled » (aucune adresse IP configurée).

**`show version`**

![show version](captures/p1-e2-i-show-version.png)

- Version IOS : `16.3.2`
- Image système : `cat3k_caa-universalk9.16.03.02.SPA.bin`
- Adresse MAC de base : `00:60:2F:DE:17:2D`

**`show interface gig1/0/6`**

![show interface gig1/0/6](captures/p1-e2-j-interface-gig.png)

- État : activée (`up, line protocol is up (connected)`).
- Ce qui peut la désactiver : câble débranché ou défectueux, panne matérielle, `shutdown` administratif.
- Adresse MAC : `000c.8589.1806`
- Vitesse / duplex : `Full-duplex, 100 Mb/s`

**`show vlan`**

![show vlan](captures/p1-e2-k-show-vlan.png)

- Nom du VLAN 1 : `default`
- Ports : tous les ports du commutateur (Gi1/0/1 à Gi1/0/24 et Gi1/1/1 à Gi1/1/4).
- Actif : oui.
- Type : `enet`

**`dir flash:`**

![flash](captures/p1-e2-l-flash.png)

- Image IOS : `cat3k_caa-universalk9.16.03.02.SPA.bin`

---

## Partie 2 : Configuration de base

### Configuration de S1

```text
enable
configure terminal

! Paramètres de base
no ip domain-lookup
hostname S1
service password-encryption
enable secret class
banner motd #Unauthorized access is strictly prohibited.#

! VLAN de gestion et SVI
vlan 99
interface vlan 99
 ip address 192.168.1.2 255.255.255.0
 ipv6 address FE80::2 link-local
 ipv6 address 2001:DB8:ACAD:1::2/64
 no shutdown

! Ports utilisateur dans le VLAN 99
interface range gigabitEthernet 1/0/1 - 24
 switchport mode access
 switchport access vlan 99

ip default-gateway 192.168.1.1

! Accès console
line con 0
 password cisco
 login
 logging synchronous

! Accès distant (Telnet)
line vty 0 15
 password cisco
 login

end
copy running-config startup-config
```


**Configuration saisie sur S1**

![config S1](captures/p2-e1-config-s1.png)

**`show vlan brief`**

![show vlan brief](captures/p2-e1-d-vlan-brief.png)

**Rôle de `login`** : il oblige le commutateur à demander le mot de passe configuré ; sans cette commande, la ligne n'authentifie pas l'utilisateur.

### Étape 2 – Configuration IP de PC-A

![ip config PC-A](captures/p2-e2-pc-a-ip.png)

---

## Partie 3 : Vérification et tests

### Étape 1 – Configuration du commutateur

**`show run`**

![show run](captures/p3-e1-a-show-run.png)

**`show interface vlan 99`**

![show interface vlan 99](captures/p3-e1-b-interface-vlan99.png)

- Bande passante : 1 000 000 Kbit/s
- État du VLAN 99 : `up`
- État du protocole de ligne : `up`

### Étape 2 – Tests `ping`

```text
ping 192.168.1.2
ping 2001:db8:acad:1::2
```

![ping](captures/p3-e2-ping.png)

### Étape 3 – Gestion à distance (Telnet)

![Telnet](https://github.com/richmondklab/configuration_basique_switch/blob/main/capture%20telnet.png?raw=true)

### Étape 4 – Déploiement de S1

![rack](captures/p3-e4-rack.png)

---

## Questions de réflexion

1. **Mot de passe vty** : sans lui, l'accès Telnet est impossible ; avec lui, on limite l'accès distant aux personnes autorisées.
2. **Changer le VLAN 1** : c'est le VLAN par défaut, connu de tous et visé par les attaques ; un VLAN dédié sépare le trafic de gestion et réduit l'exposition.
3. **Éviter les mots de passe en clair** : `service password-encryption` pour la configuration, et surtout **SSH** à la place de Telnet, qui chiffre les échanges.


