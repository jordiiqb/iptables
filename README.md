# PRÀCTICA IPTABLES

### Exercici 00

```bash
#! /bin/bash
# @edt ASIX M11-SAD Curs 2018-2019
# iptables

#echo 1 > /proc/sys/ipv4/ip_forward

# Regles flush
iptables -F
iptables -X
iptables -Z
iptables -t nat -F

# Polítiques per defecte: 
iptables -P INPUT ACCEPT
iptables -P OUTPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -t nat -P PREROUTING ACCEPT
iptables -t nat -P POSTROUTING ACCEPT

# obrir el localhost
iptables -A INPUT  -i lo -j ACCEPT
iptables -A OUTPUT -o lo -j ACCEPT

# obrir la nostra ip
iptables -A INPUT -s 192.168.0.18 -j ACCEPT
iptables -A OUTPUT -d 192.168.0.18 -j ACCEPT
```

Aquest script esborra les regles que hi han predefinides fent un flush, després establim com política acceptar totes les connexions, activem el nat, obrim les connexions per les interfícies per al loopback i  per a la nostra ip del host local.

### Exercici 01

```bash
#! /bin/bash
# @edt ASIX M11-SAD Curs 2018-2019
# iptables

#echo 1 > /proc/sys/ipv4/ip_forward

# Regles flush
iptables -F
iptables -X
iptables -Z
iptables -t nat -F

# Polítiques per defecte: 
iptables -P INPUT ACCEPT
iptables -P OUTPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -t nat -P PREROUTING ACCEPT
iptables -t nat -P POSTROUTING ACCEPT

# obrir el localhost
iptables -A INPUT  -i lo -j ACCEPT
iptables -A OUTPUT -o lo -j ACCEPT

# obrir la nostra ip
iptables -A INPUT -s 192.168.0.18 -j ACCEPT
iptables -A OUTPUT -d 192.168.0.18 -j ACCEPT

# Xapat ports 80... ############################
# port 80 obert a tothom
iptables -A INPUT -p tcp --dport 80 -j ACCEPT
# port 2080 tancat a tothom: reject
iptables -A INPUT -p tcp --dport 2080 -j REJECT
# port 3080 tancat a tothom: drop
iptables -A INPUT -p tcp --dport 3080 -j DROP
# port 4080 tancat a tothom exceprte i26
iptables -A INPUT -p tcp --dport 4080 -s 192.168.2.56  -j ACCEPT
iptables -A INPUT -p tcp --dport 4080 -j DROP
# port 5080 obert a tothom tancat a i26
iptables -A INPUT -p tcp --dport 5080 -s 192.168.2.56 -j REJECT
iptables -A INPUT -p tcp --dport 5080 -j ACCEPT
# port 6080 tancat a tohom, obert a hisx2 tancat a i26
iptables -A INPUT -p tcp --dport 6080 -s 192.168.2.56 -j REJECT
iptables -A INPUT -p tcp --dport 6080 -s 192.168.2.0/24 -j ACCEPT
iptables -A INPUT -p tcp --dport 6080 -j DROP
# port 7080 obert a tothom, tancat a xarxa hisx2 i obert i26
iptables -A INPUT -p tcp --dport 7080 -s 192.168.2.56 -j ACCEPT
iptables -A INPUT -p tcp --dport 7080 -s 192.168.2.0/24 -j DROP
iptables -A INPUT -p tcp --dport 7080 -j ACCEPT
# tancar tot accés a ports del rang 3000:8000
#iptables -A INPUT -p tcp --dport 3000:8000 -j REJECT
# barreres finals de tancar (ull quedar tancat!)
#iptables -A INPUT -p tcp --dport 1:1024 -j REJECT
##############################################
```

Aquest script executa les mateixes restriccions que en el cas anterior, però, també obre el tràfic d'entrada al nostre ordinador per a diferents ports acabants en 80 (posiblement relacionats amb el protocol http). Per últim, tanca totes les connexions entrants als ports desde el 3000 fins al 8000, i també a tots els "well-known" ports.

### Exercici 02

```bash
  
#! /bin/bash
# @edt ASIX M11-SAD Curs 2018-2019
# iptables

#echo 1 > /proc/sys/ipv4/ip_forward

# Regles flush
iptables -F
iptables -X
iptables -Z
iptables -t nat -F

# Polítiques per defecte: 
iptables -P INPUT ACCEPT
iptables -P OUTPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -t nat -P PREROUTING ACCEPT
iptables -t nat -P POSTROUTING ACCEPT

# obrir el localhost
iptables -A INPUT  -i lo -j ACCEPT
iptables -A OUTPUT -o lo -j ACCEPT

# obrir la nostra ip
iptables -A INPUT -s 192.168.0.18 -j ACCEPT
iptables -A OUTPUT -d 192.168.0.18 -j ACCEPT


# Exemples de regles output
################################################
# accedir a qualsevol port/destí
  #iptables -A OUTPUT -j ACCEPT
# accedir a port 13 de qualsevol destí
  #iptables -A OUTPUT -p tcp --dport 13 -j ACCEPT
  #iptables -A OUTPUT -p tcp --dport 13 -d 0.0.0.0/0  -j ACEEPT
# accedir a qualsevol port 2013 excepte el del i26
  #iptables -A OUTPUT -p tcp --dport 2013 -d 192.168.2.56 -j REJECT
  #iptables -A OUTPUT -p tcp --dport 2013 -j ACCEPT
# denegat accedir a qualsevol port 3013, però si al del i26
  #iptables -A OUTPUT -p tcp --dport 3013 -d 192.168.2.56 -j ACCEPT
  #iptables -A OUTPUT -p tcp --dport 3013 -j REJECT
# obert acces 4013 a tothom, tancar a hisx2 i obert a i26
  #iptables -A OUTPUT -p tcp --dport 4013 -d 192.168.2.56 -j ACCEPT
  #iptables -A OUTPUT -p tcp --dport 4013 -d 192.168.2.0/24 -j REJECT
  #iptables -A OUTPUT -p tcp --dport 4013 -j ACCEPT
# xapar accés a qualsevol dels ports 80, 13 i 7
  #iptables -A OUTPUT -p tcp --dport 80 -j REJECT
  #iptables -A OUTPUT -p tcp --dport 13 -j REJECT
  #iptables -A OUTPUT -p tcp --dport 7  -j REJECT
# no permetre accedir al host i26 ni al i27
 #iptables -A OUTPUT -d 192.168.2.56 -j REJECT
 #iptables -A OUTPUT -d 192.168.2.57 -j REJECT
# no permetre accedir a les xarxes hisx1 i hisx2
 #iptables -A OUTPUT -d 192.168.2.0/24 -j REJECT
 #iptables -A OUTPUT -d 192.168.1.0/24 -j REJECT
# a la xarxa hisx2 no s'hi pot accedir excepte per ssh
iptables -A OUTPUT -p tcp --dport 22 -d 192.168.2.0/24 -j ACCEPT
iptables -A OUTPUT -d 192.168.2.0/24 -j REJECT
```

Aquest script manté la configuració inicial del primer script, i a més a més, tanquem totes les connexions que surten de la màquina host per al port 13 i relacionats amb aquest, així com també els ports 80 i 7. També, tanca totes les connexions als hosts i26 i i27, així com tyambé a les xarxes hisx1 i hisx2 (a aquesta última però, es pot accedir mitjançant ssh).

### Exercici 03

```bash
#! /bin/bash
# @edt ASIX M11-SAD Curs 2018-2019
# iptables

#echo 1 > /proc/sys/ipv4/ip_forward

# Regles flush
iptables -F
iptables -X
iptables -Z
iptables -t nat -F

# Polítiques per defecte: 
iptables -P INPUT ACCEPT
iptables -P OUTPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -t nat -P PREROUTING ACCEPT
iptables -t nat -P POSTROUTING ACCEPT

# obrir el localhost
iptables -A INPUT  -i lo -j ACCEPT
iptables -A OUTPUT -o lo -j ACCEPT

# obrir la nostra ip
iptables -A INPUT -s 192.168.0.18 -j ACCEPT
iptables -A OUTPUT -d 192.168.0.18 -j ACCEPT


# Regles amb trafic related, established
###################################################
# permetre navegar web (mal feta)
  #iptables -A OUTPUT -p tcp --dport 80 -j ACCEPT
  #iptables -A INPUT  -p tcp --sport 80 -j ACCEPT
# filtrant tràfic només de resposta
  #iptables -A OUTPUT -p tcp --dport 80 -j ACCEPT
  #iptables -A INPUT  -p tcp --sport 80 -m tcp -m state \
  #	  --state RELATED,ESTABLISHED  -j ACCEPT
# oferir el servei web, permetre només respostes
# a peticions establertes.
  #iptables -A OUTPUT -p tcp --sport 80 -m tcp -m state \
  #        --state RELATED,ESTABLISHED -j ACCEPT   
  #iptables -A INPUT -p tcp --dport 80 -j ACCEPT
# oferim el servei web a tothom menys al i26
  iptables -A OUTPUT -p tcp --sport 80 -m tcp -m state \
          --state RELATED,ESTABLISHED -j ACCEPT
  iptables -A INPUT -p tcp --dport 80 -s 192.168.2.56 -j REJECT
  iptables -A INPUT -p tcp --dport 80 -j ACCEPT
```

Aquest script manté la configuració inicial del primer script, i a més a més, només permeteix la connexió de sortida pel port 80 amb altres hosts amb els que hem establert la connexió anteriorment. També, no deixa passar el tràfic d'entrada pel port 80 al host amb ip 192.168.2.56.

### Exercici 04

```bash
#! /bin/bash
# @edt ASIX M11-SAD Curs 2018-2019
# iptables

#echo 1 > /proc/sys/ipv4/ip_forward

# Regles flush
iptables -F
iptables -X
iptables -Z
iptables -t nat -F

# Polítiques per defecte: 
iptables -P INPUT ACCEPT
iptables -P OUTPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -t nat -P PREROUTING ACCEPT
iptables -t nat -P POSTROUTING ACCEPT

# obrir el localhost
iptables -A INPUT  -i lo -j ACCEPT
iptables -A OUTPUT -o lo -j ACCEPT

# obrir la nostra ip
iptables -A INPUT -s 192.168.0.18 -j ACCEPT
iptables -A OUTPUT -d 192.168.0.18 -j ACCEPT

# ICMP  echo-requets:8, echo-reply:0
############################################
# No permetre fer pings cap a l'exterior
  #iptables -A OUTPUT -p icmp --icmp-type 8 -j DROP
# No podem fer pings cap al i26
  #iptables -A OUTPUT -p icmp --icmp-type 8 -d 192.168.2.56 -j DROP
# No permetem respondre als pings que ens facin
  #iptables -A OUTPUT -p icmp --icmp-type 0 -j DROP
# No permetem rebre respostes de ping
  iptables -A INPUT -p icmp --icmp-type 0 -j DROP
```

Aquest script manté la configuració inicial del primer script, i a més a més, prohibim fer ping desde el host cap a l'exterior, desde el host a la ip 192.168.2.56 i tampoc respondre als pings que ens facin.

### Exercici 05

```bash
#! /bin/bash
# @edt ASIX M11-SAD Curs 2018-2019
# iptables

#echo 1 > /proc/sys/ipv4/ip_forward

# Regles flush
iptables -F
iptables -X
iptables -Z
iptables -t nat -F

# Polítiques per defecte: 
iptables -P INPUT ACCEPT
iptables -P OUTPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -t nat -P PREROUTING ACCEPT
iptables -t nat -P POSTROUTING ACCEPT

# obrir el localhost
iptables -A INPUT  -i lo -j ACCEPT
iptables -A OUTPUT -o lo -j ACCEPT

# obrir la nostra ip
iptables -A INPUT -s 192.168.0.18 -j ACCEPT
iptables -A OUTPUT -d 192.168.0.18 -j ACCEPT

# Fer NAT per les xarxes internes:
# - 172.19.0.0/24
# - 172.20.0.0/24
iptables -t nat -A POSTROUTING -s 172.19.0.0/24 -o enp6s0 -j MASQUERADE
iptables -t nat -A POSTROUTING -s 172.20.0.0/24 -o enp6s0 -j MASQUERADE

```

Aquest script manté la configuració inicial del primer script, i a més a més, activem l'opció de fer NAT i així la màquina host traduirà i habilitarà les connexions amb les xarxes internes.

### Exercici 06

```bash
#! /bin/bash
# @edt ASIX M11-SAD Curs 2018-2019
# iptables

#echo 1 > /proc/sys/ipv4/ip_forward

# Regles flush
iptables -F
iptables -X
iptables -Z
iptables -t nat -F

# Polítiques per defecte: 
iptables -P INPUT ACCEPT
iptables -P OUTPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -t nat -P PREROUTING ACCEPT
iptables -t nat -P POSTROUTING ACCEPT

# obrir el localhost
iptables -A INPUT  -i lo -j ACCEPT
iptables -A OUTPUT -o lo -j ACCEPT

# obrir la nostra ip
iptables -A INPUT -s 192.168.0.18 -j ACCEPT
iptables -A OUTPUT -d 192.168.0.18 -j ACCEPT

# Fer NAT per les xarxes internes:
# - 172.19.0.0/24
# - 172.20.0.0/24
iptables -t nat -A POSTROUTING -s 172.19.0.0/24 -o enp6s0 -j MASQUERADE
iptables -t nat -A POSTROUTING -s 172.20.0.0/24 -o enp6s0 -j MASQUERADE


# Exemples FORWARD
# ####################################################
# xarxa A no pot accedir a xarxaB
  #iptables -A FORWARD -s 172.19.0.0/16 -d 172.20.0.0/16 -j REJECT
# xarxaA no pot accedir a B2
  #iptables -A FORWARD -i br-6392860b3d9e -d 172.20.0.3 -j REJECT
  #iptables -A FORWARD -s 172.19.0.0/16 -d 172.20.0.3 -j REJECT
# host A1 no pot connectar al host B1
  #iptables -A FORWARD -s 172.19.0.2 -d 172.20.0.2 -j REJECT
# xarxaA no pot accedir a ports 13 (de enlloc) 
  #iptables -A FORWARD -p tcp -s 172.19.0.0/24 --dport 13 -j REJECT
# xarxaA no pot accedir a ports 2013 de la xarxaB
  #iptables -A FORWARD -p tcp -s 172.19.0.0/24 -d 172.20.0.0/24 \
  ##	              --dport 2013 -j REJECT
# xarxaA permetre navegar per internet però res més a l'exterior
#  iptables -A FORWARD -s 172.19.0.0/24 -o enp6s0 -p tcp --dport 80 \
#	   -j ACCEPT
#  iptables -A FORWARD -d 172.19.0.0/24 -p tcp --sport 80 -i enp6s0 \
#           -m state --state RELATED,ESTABLISHED -j ACCEPT
#  iptables -A FORWARD -s 172.19.0.0/24 -o enp6s0 -j REJECT 
#  iptables -A FORWARD -d 172.19.0.0/24 -i enp6s0 -j REJECT
# xarxaA accedir port 2013 de totes les xarxes d'internet 
# excepte de la xarxa hisx2
#   iptables -A FORWARD -s 172.19.0.0/24 -d 192.168.2.0/24 \
#	    -p tcp --dport 2013 -o enp6s0 -j REJECT
#   iptables -A FORWARD -s 172.19.0.0/24 -p tcp --dport 2013 \
#	    -o enp6s0  -j ACCEPT
# evitar que es falsifiqui la ip de origen: SPOOFING
iptable -A FORWARD  ! -s 172.19.0.0/24 -i br-7d521247ea41 -j DROP

```

Aquest script manté la configuració inicial del primer script, i a més a més, activem l'opció de fer NAT i l'opció de fer forwarding. Així, tots els paquets que arrivin al host podràn ser encaminats on toqui.

### Exercici 07

```bash
#! /bin/bash
# @edt ASIX M11-SAD Curs 2018-2019
# iptables

#echo 1 > /proc/sys/ipv4/ip_forward

# Regles flush
iptables -F
iptables -X
iptables -Z
iptables -t nat -F

# Polítiques per defecte: 
iptables -P INPUT ACCEPT
iptables -P OUTPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -t nat -P PREROUTING ACCEPT
iptables -t nat -P POSTROUTING ACCEPT

# obrir el localhost
iptables -A INPUT  -i lo -j ACCEPT
iptables -A OUTPUT -o lo -j ACCEPT

# obrir la nostra ip
iptables -A INPUT -s 192.168.0.18 -j ACCEPT
iptables -A OUTPUT -d 192.168.0.18 -j ACCEPT

# Fer NAT per les xarxes internes:
# - 172.19.0.0/24
# - 172.20.0.0/24
iptables -t nat -A POSTROUTING -s 172.19.0.0/24 -o enp6s0 -j MASQUERADE
iptables -t nat -A POSTROUTING -s 172.20.0.0/24 -o enp6s0 -j MASQUERADE


# Exemples port forwarding
#iptables -A FORWARD -p tcp --dport 13 -j REJECT
#iptables -A INPUT -p tcp --dport 13 -j REJECT
#iptables -t nat -A PREROUTING -p tcp --dport 5001 -j DNAT \
#	  --to 172.19.0.2:13
#iptables -t nat -A PREROUTING -p tcp --dport 5002 -j DNAT \
#          --to 172.19.0.3:13
#iptables -t nat -A PREROUTING -p tcp --dport 5003 -j DNAT \
#          --to :13
#iptables -t nat -A PREROUTING -p tcp --dport 6001 -j DNAT \
#	   --to 172.19.0.2:80
#iptables -t nat -A PREROUTING -p tcp --dport 6002 -j DNAT \
#           --to 10.1.1.0:80
#iptables -t nat -A PREROUTING -s 192.168.2.56 -p tcp \
#	   --dport 6000 -j DNAT --to :22
#iptables -t nat -A PREROUTING -s 172.19.0.0/24 -p tcp \
#          --dport 25 -j DNAT --to 192.168.2.56:25
#iptables -t nat -A PREROUTING -s 172.20.0.0/24 -p tcp \
#          --dport 25 -j DNAT --to 192.168.2.56:25
iptables -t nat -A PREROUTING -s 172.19.0.0/24 -p tcp \
          --dport 80 -j DNAT --to 192.168.2.56:80
iptables -t nat -A PREROUTING -s 172.20.0.0/24 -p tcp \
          --dport 80 -j DNAT --to 10.1.1.8:80

```

Aquest script manté la configuració inicial del primer script, i a més a més, activem l'opció de fer NATi port forwarding. El que fa aquesta configuració és que la informació de destinació de tots els paquest rebuts al host es canviada per una altra indicada. En el cas d'aquest excerici, tots els paquest entrants a la xarxa 172.19.0.0 desde el port 80 son redireccionats al mateix port de la ip 192.168.2.556. Ídem amb la xarxa 172.20.0.0 i la ip 10.1.1.8.


### Exercici 08

```bash
  
#! /bin/bash
# @edt ASIX M11-SAD Curs 2018-2019
# iptables

#echo 1 > /proc/sys/ipv4/ip_forward

# Regles flush
iptables -F
iptables -X
iptables -Z
iptables -t nat -F

# Polítiques per defecte: 
iptables -P INPUT ACCEPT
iptables -P OUTPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -t nat -P PREROUTING ACCEPT
iptables -t nat -P POSTROUTING ACCEPT

# obrir el localhost
iptables -A INPUT  -i lo -j ACCEPT
iptables -A OUTPUT -o lo -j ACCEPT

# obrir la nostra ip
iptables -A INPUT -s 192.168.0.18 -j ACCEPT
iptables -A OUTPUT -d 192.168.0.18 -j ACCEPT

# Fer NAT per les xarxes internes:
# - 172.19.0.0/24
# - 172.20.0.0/24
iptables -t nat -A POSTROUTING -s 172.19.0.0/24 -o enp6s0 -j MASQUERADE
iptables -t nat -A POSTROUTING -s 172.20.0.0/24 -o enp6s0 -j MASQUERADE
iptables -t nat -A POSTROUTING -s 172.21.0.0/24 -o enp6s0 -j MASQUERADE

# Regles de DMZ
# ###########################################################

# necessari per al funcionament de l'exercici (4)
iptables -A FORWARD  -d 172.19.0.2 -i enp6s0 -p tcp --dport 80 -j ACCEPT  #**2**
iptables -A FORWARD  -s 172.19.0.2 -o enp6s0 -p tcp --sport 80 \
	    -m state --state ESTABLISHED,RELATED -j ACCEPT  #**2**
iptables -A FORWARD  -d 172.19.0.3 -i enp6s0 -p tcp --dport 2013 -j ACCEPT  #**2**
iptables -A FORWARD  -s 172.19.0.3 -o enp6s0 -p tcp --sport 2013 \
            -m state --state ESTABLISHED,RELATED -j ACCEPT  #**2**
# necessari per al funcionament de l'exercici (5)
iptables -A FORWARD  -d 172.19.0.0/16 -i enp6s0 -p tcp --dport 22 -j ACCEPT  #**3**
iptables -A FORWARD  -s 172.19.0.0/16 -o enp6s0 -p tcp --sport 22 \
            -m state --state ESTABLISHED,RELATED -j ACCEPT  #**3**

# (1) de la xarxaA només es pot accedir del router/fireall 
# als serveis: ssh i  daytime(13)
iptables -A INPUT -s 172.19.0.0/16 -p tcp --dport 22 -j ACCEPT  # -i br-xxx
iptables -A INPUT -s 172.19.0.0/16 -p tcp --dport 13 -j ACCEPT  # -i br-xxx
iptables -A INPUT -s 172.19.0.0/16 -j REJECT  # -i br-xxx

# (2) de la xarxaA només es pot accedir a l'exterior als serveis 
# web, ssh i daytime(2013)
iptables -A FORWARD  -s 172.19.0.0/16 -p tcp --dport 80 \
    	    -o enp6s0   -j ACCEPT
iptables -A FORWARD  -d 172.19.0.0/16 -p tcp --sport 80 \
            -i enp6s0 -m state --state RELATED,ESTABLISHED -j ACCEPT
iptables -A FORWARD  -s 172.19.0.0/16 -p tcp --dport 22 \
            -o enp6s0   -j ACCEPT
iptables -A FORWARD  -d 172.19.0.0/16 -p tcp --sport 22 \
            -i enp6s0 -m state --state RELATED,ESTABLISHED -j ACCEPT
iptables -A FORWARD  -s 172.19.0.0/16 -p tcp --dport 2013 \
            -o enp6s0   -j ACCEPT
iptables -A FORWARD  -d 172.19.0.0/16 -p tcp --sport 2013 \
            -i enp6s0 -m state --state RELATED,ESTABLISHED -j ACCEPT
iptables -A FORWARD  -s 172.19.0.0/16 -o enp6s0 -j REJECT  #**1**
iptables -A FORWARD  -d 172.19.0.0/16 -i enp6s0 -j REJECT  #**1**

# (3) de la xarxaA només es pot accedir serveis que ofereix la 
# DMZ al servei web
iptables -A FORWARD -s 172.19.0.0/16 -d 172.21.0.0/16 -p tcp \
	    --dport 80 -j ACCEPT
iptables -A FORWARD -s 172.19.0.0/16 -d 172.21.0.0/16 -j REJECT

# (4) redirigir els ports perquè des de l'exterior es tingui 
# accés a: 3001->hostA1:80, 3002->hostA2:2013, 3003->hostB1:2080,
# 3004->hostB2:2007
iptables -t nat -A PREROUTING -i enp6s0 -p tcp --dport 3001 \
	    -j DNAT --to 172.19.0.2:80
iptables -t nat -A PREROUTING -i enp6s0 -p tcp --dport 3002 \
            -j DNAT --to 172.19.0.3:2013
          # Aquests dos primers exercicis no funcionen perquè
	  # el tràfic està tallat a la regla **1**
	  # caldria rectificar les regles **1** o escriure abans
	  # una regla que permeti el tràfic exterior al port
	  # 80 dels hosta A1 i A2 **3**
iptables -t nat -A PREROUTING -i enp6s0 -p tcp --dport 3003 \
            -j DNAT --to 172.20.0.2:2080
iptables -t nat -A PREROUTING -i enp6s0 -p tcp --dport 3004 \
            -j DNAT --to 172.20.0.3:3013

# (5) S'habiliten els ports 4001 en endavant per accedir per ssh als ports ssh de: hostA1(4001), hostA2(4002), hostB1(4003), hostB2(4004).
iptables -t nat -A PREROUTING -i enp6s0 -p tcp --dport 4001 \
            -j DNAT --to 172.19.0.2:22
iptables -t nat -A PREROUTING -i enp6s0 -p tcp --dport 4002 \
            -j DNAT --to 172.19.0.3:22
            # torna a passar el mateix que l'exercici anterior,
	    # les regles de l'exercici (2) marcades **1**
	    # tanquen el trafic forward del port 22 de exterior
	    # a xarxaA. Cal obrir-les abans **3**
iptables -t nat -A PREROUTING -i enp6s0 -p tcp --dport 4003 \
            -j DNAT --to 172.20.0.2:22
iptables -t nat -A PREROUTING -i enp6s0 -p tcp --dport 4004 \
            -j DNAT --to 172.20.0.3:22

# (6) s'habilita el port 4000 per accedir al port ssh del 
# router/firewal si la ip origen és del host i26.
iptables -t nat -A PREROUTING -i enp6s0 -p tcp --dport 4000 \
            -s 192.168.2.56 -j DNAT --to :22

# (7) els hosts de la xarxaB tenen accés a tot arreu excepte
# a la xarxaA.
iptables -A FORWARD -s 172.20.0.0/16  -d 172.19.0.0/16 -j DROP
iptables -A FORWARD -s 172.20.0.0/16 -j ACCEPT
iptables -A FORWARD -d 172.20.0.0/16 -j ACCEPT
```

En aquest exercici, a part de la configuració per defecte, creem una nova configuració per a una xarxa "Demilitarized Zone" que estarà aïllada de les altres xarxes. La xarxa A, només es pot accedir per el firewall i aquesta només pot accedir a determinats serrveis del exterior i de la zona DMZ. La xraxa B pot accedir a tot arreu excepte a la xarxa A. S'habilita el NAT per accedir a determinats serveis com el ssh dels diferents hosts de les xarxes A i B.

### Exercici 09

```bash
#! /bin/bash
# @edt ASIX M11-SAD Curs 2018-2019
# iptables

#echo 1 > /proc/sys/ipv4/ip_forward

# Regles flush
iptables -F
iptables -X
iptables -Z
iptables -t nat -F

# Polítiques per defecte: 
iptables -P INPUT ACCEPT
iptables -P OUTPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -t nat -P PREROUTING ACCEPT
iptables -t nat -P POSTROUTING ACCEPT

# obrir el localhost
iptables -A INPUT  -i lo -j ACCEPT
iptables -A OUTPUT -o lo -j ACCEPT

# obrir la nostra ip
iptables -A INPUT -s 192.168.0.18 -j ACCEPT
iptables -A OUTPUT -d 192.168.0.18 -j ACCEPT

# Fer NAT per les xarxes internes:
# - 172.19.0.0/24
# - 172.20.0.0/24
iptables -t nat -A POSTROUTING -s 172.19.0.0/24 -o enp6s0 -j MASQUERADE
iptables -t nat -A POSTROUTING -s 172.20.0.0/24 -o enp6s0 -j MASQUERADE
iptables -t nat -A POSTROUTING -s 172.21.0.0/24 -o enp6s0 -j MASQUERADE

# Regles de DMZ
# ###########################################################

# (1) des d'un host exterior accedir al servei ldap de la DMZ.
iptables -t nat -A PREROUTING -p tcp --dport 389 -i enp6s0 -j DNAT \
         	--to 172.21.0.6:389
iptables -t nat -A PREROUTING -p tcp --dport 636 -i enp6s0 -j DNAT \
                --to 172.21.0.6:636

# (2) des d'un host exterior, engegar un container kclient i 
# obtenir un tiket kerberos del servidor de la DMZ.
iptables -t nat -A PREROUTING -p tcp --dport 88 -i enp6s0 -j DNAT \
                --to 172.21.0.4:88
iptables -t nat -A PREROUTING -p tcp --dport 543 -i enp6s0 -j DNAT \
                --to 172.21.0.4:543
iptables -t nat -A PREROUTING -p tcp --dport 749 -i enp6s0 -j DNAT \
                --to 172.21.0.4:749
iptables -t nat -A PREROUTING -p tcp --dport 544 -i enp6s0 -j DNAT \
                --to 172.21.0.4:544

# (3) des d'un host exterior emuntar un recurs samba del servidor 
# de la DMZ. Ports: 
iptables -t nat -A PREROUTING -p tcp --dport 139 -i enp6s0 -j DNAT \
                --to 172.21.0.5:139
iptables -t nat -A PREROUTING -p tcp --dport 445 -i enp6s0 -j DNAT \
                --to 172.21.0.5:445

```

En aquest exercici també a part de la configuració per defecte, creem una nova configuració per a una xarxa "Demilitarized Zone" que estarà aïllada de les altres xarxes. Amb les primeres línies dela configuració habilitem que es pugui accedir desde fora al server ldap de la zona DMZ. També habilitem que es pugui obtenir un ticket de kerberos i muntar un recurs de samba.

### Exercici 11

```bash
#! /bin/bash
# @edt ASIX M11-SAD Curs 2018-2019
# iptables

#echo 1 > /proc/sys/ipv4/ip_forward

# Regles flush
iptables -F
iptables -X
iptables -Z
iptables -t nat -F

# Polítiques per defecte: 
iptables -P INPUT ACCEPT
iptables -P OUTPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -t nat -P PREROUTING ACCEPT
iptables -t nat -P POSTROUTING ACCEPT

# obrir el localhost
iptables -A INPUT  -i lo -j ACCEPT
iptables -A OUTPUT -o lo -j ACCEPT

# obrir la nostra ip
iptables -A INPUT -s 192.168.2.56 -j ACCEPT
iptables -A OUTPUT -d 192.168.2.56 -j ACCEPT

# (1) Fer NAT per les xarxes internes:
iptables -t nat -A POSTROUTING -s 172.200.0.0/24 -o enp5s0 -j MASQUERADE

# (2) Obrir el port 3001/3002 per accedir servei daytime-hostA i echo-hostB
iptables -t nat -A PREROUTING -p tcp --dport 3001 -i enp5s0 -j DNAT --to 172.200.0.2:13
iptables -t nat -A PREROUTING -p tcp --dport 3002 -i enp5s0 -j DNAT --to 172.200.0.3:7

# (3) Obrir els ports 4001/4002 per accedir als ssh
iptables -t nat -A PREROUTING -p tcp --dport 4001 -i enp5s0 -j DNAT --to 172.200.0.2:22
iptables -t nat -A PREROUTING -p tcp --dport 4002 -i enp5s0 -j DNAT --to 172.200.0.3:22

# (4) Als hosts de la xarxa privada interna se'ls permet navegar per internet,
#     però no cap altre accés a internet.
iptables -A FORWARD -s 172.200.0.0/16 -p tcp --dport 80 -o enp5s0 -j ACCEPT
iptables -A FORWARD -d 172.200.0.0/16 -p tcp --sport 80 -i enp5s0 -j ACCEPT
#iptables -A FORWARD -s 172.200.0.0/16 -o enp5s0 -j DROP  # talla tot el tràfic: tcp, udp icmp!!
#iptables -A FORWARD -d 172.200.0.0/16 -i enp5s0 -j DROP  # <idem>
iptables -A FORWARD -s 172.200.0.0/16 -p tcp -o enp5s0 -j DROP 
iptables -A FORWARD -s 172.200.0.0/16 -p udp -o enp5s0 -j DROP
iptables -A FORWARD -d 172.200.0.0/16 -p tcp -i enp5s0 -j DROP  
iptables -A FORWARD -d 172.200.0.0/16 -p udp -i enp5s0 -j DROP  

# (5) No es permet que els hosts de la xarxa interna facin ping a l'exterior.
iptables -A FORWARD -s 172.200.0.0/16 -p icmp --icmp-type=8 -o enp5s0 -j DROP

# (6) El router no contesta als pings que rep, però si que pot fer ping.
iptables -A INPUT  -p icmp --icmp-type=8 -j DROP
iptables -A OUTPUT -p icmp --icmp-type=0 -j DROP
```

En aquest exercici, apliquem la configuració per defecte, i a més a més, activem el NATper a les xarxes internes i així poder fer connexió a diferenst servies com daytime, ssh, etc. a través d'altres ports. A part, es tanquen totes les connexions tcp i udp a internet per a totes les xarxes excpete a la 172.200.0.0. Per últim, no es permet que es faci ping a l'exterior desde l'anterior xarxa i fem que el router no contesti als pings entrants però que si que pugui fer ping.








