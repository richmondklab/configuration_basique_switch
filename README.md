# TP Cisco – Configuration de base d'un commutateur (SVI, VLAN 99, accès distant)

Travaux pratiques réalisés avec **Cisco Packet Tracer** : câblage d'un petit réseau, configuration de base d'un commutateur Cisco, puis vérification de la connectivité et de la gestion à distance.

## Objectifs

1. Câbler le réseau et vérifier la configuration par défaut du commutateur
2. Configurer les paramètres de base des périphériques réseau
3. Vérifier et tester la connectivité réseau

 ![Image Alt](https://github.com/richmondklab/configuration_basique_switch/blob/main/table%20d'adressage.png?raw=true)


---

## Partie 1 : Câblage et configuration par défaut

### Étape 1 – Câblage

![Image Alt](https://github.com/richmondklab/configuration_basique_switch/blob/main/cablage.png?raw=true)




![Image Alt](https://github.com/richmondklab/configuration_basique_switch/blob/main/capture%20telnet.png?raw=true)


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


**Capture : configuration saisie sur S1**

![config S1](captures/p2-e1-config-s1.png)

**`show vlan brief`**

![show vlan brief](captures/p2-e1-d-vlan-brief.png)

**Réponse (Étape 1 g) :**

&nbsp;

### Étape 2 – Configuration IP de PC-A

![ip config PC-A](captures/p2-e2-pc-a-ip.png)

---

## Partie 3 : Vérification et tests

### Étape 1 – Configuration du commutateur

**`show run`**

![show run](captures/p3-e1-a-show-run.png)

**`show interface vlan 99`**

![show interface vlan 99](captures/p3-e1-b-interface-vlan99.png)

- Réponse 1 :

  &nbsp;

- Réponse 2 :

  &nbsp;

- Réponse 3 :

  &nbsp;

### Étape 2 – Tests `ping`

```text
ping 192.168.1.2
ping 2001:db8:acad:1::2
```

![ping](captures/p3-e2-ping.png)

### Étape 3 – Gestion à distance (Telnet)

![telnet](captures/p3-e3-telnet.png)

### Étape 4 – Déploiement de S1

![rack](captures/p3-e4-rack.png)

---

## Questions de réflexion

**1.**

&nbsp;

**2.**

&nbsp;

**3.**

&nbsp;

---


