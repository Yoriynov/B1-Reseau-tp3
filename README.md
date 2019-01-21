# B1 Réseau 2018 - TP3
## I. Création et utilisation simples d'une VM CentOS
### 4. Configuration réseau d'une machine CentOS
#### A FAIRE :

a. utilisez une commande pour prouver que vous avez internet depuis la VM

J'ai utilisé la commande "Curl -SL WWW .google.com" en résultat : une page HTML lue et donc énormément de ligne ce qui prouve bien que j'ai internet car la VM à lire la Page HTML de code

b. prouvez que votre PC hôte et la VM peuvent communiquer

J'ai fait commande pour m'afficher les ips de chaque Machine, la mienne et la VM et j'ai ping lip de l'hôte à la machine et vice-versa et donc :

de l'hôte a la machine -> Statistiques Ping pour 192.168.127.1:
Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%)
De la machine a l'hôte -> 64 bytes from 10.33.3.16  icmp seq=1 Ttl = 127 times 0.541 ms 64 bytes from 10.33.3.16  icmp seq=2 Ttl = 127 times 1.13 ms 64 bytes from 10.33.3.16  icmp seq=3 Ttl = 127 times 0.560 ms etc. jusqu'à l'arrêter

c. affichez votre table de routage sur la VM et expliquez chacune des lignes

Pour Afficher La table de routage on utilise la commande : "ip route"

### 5. Faire joujou avec quelques commandes
#### A faire :

depuis la VM utilisez curl (ou wget) pour télécharger un fichier sur internet

 j'ai utilisé "curl -SL www.google.com" pour telecharger la page HTML de google.
 
depuis la VM utilisez dig pour connaître l'IP de :

J'ai utilisé la commande "sudo yum install bind-utils" pour installer la commande "dig" et donc j'ai utilisé les dig sur ynov et google et j'ai trouvé : 
ynov.com : IP -> 10.33.10.20
google.com : IP -> 10.33.10.20

## II. Notion de ports et SSH
### 1. Exploration des ports locaux
Avec la commande "ss -l4" j'obtiens : 

"[root@localhost ~]# ss -l4
Netid State      Recv-Q Send-Q          Local Address:Port                           Peer Address:Port
udp   UNCONN     0      0                           *:bootpc                                    *:*
tcp   LISTEN     0      128                         *:ssh                                       *:*
tcp   LISTEN     0      100                 127.0.0.1:smtp                                      *:*"
Avec la commande "ss -n" j'obtiens :

"[root@localhost ~]# ss -n
Netid  State      Recv-Q Send-Q    Local Address:Port                   Peer Address:Port
u_str  ESTAB      0      0                     * 20258                             * 20257
u_str  ESTAB      0      0                     * 24467                             * 24466
u_str  ESTAB      0      0                     * 24466                             * 24467
u_str  ESTAB      0      0      /run/dbus/system_bus_socket 21827                             * 21826"
Etc.

Avec la commande "ss -p" j'obtiens : 

"[root@localhost ~]# ss -p
Netid  State      Recv-Q Send-Q  Local Address:Port                   Peer Address:Port
u_str  ESTAB      0      0                   * 20258                             * 20257                 users:(("dbus-daemon",pid=2589,fd=9))
u_str  ESTAB      0      0                   * 24467                             * 24466                 users:(("master",pid=3383,fd=88))
u_str  ESTAB      0      0                   * 24466                             * 24467                 users:(("master",pid=3383,fd=87))
u_str  ESTAB      0      0      /run/dbus/system_bus_socket 21827                             * 21826                 users:(("dbus-daemon",pid=2589,fd=19))"
Etc.

Donc une option pour Lister les ports en écoute, avec leurs numéro et connaître l'application qui écoute sur ce ports : 
J'utilise la commande : "ss -l4 -n -p"
"[root@localhost ~]# ss -l4 -n -p
Netid  State      Recv-Q Send-Q    Local Address:Port                   Peer Address:Port
udp    UNCONN     0      0                     *:68                                *:*                   users:(("dhclient",pid=2912,fd=6))
tcp    LISTEN     0      128                   *:22                                *:*                   users:(("sshd",pid=3140,fd=3))
tcp    LISTEN     0      100           127.0.0.1:25                                *:*                   users:(("master",pid=3383,fd=13))"

Et on trouve bien un port 22 qui écoute.

### 2. SSH
Pour mettre le SSH ou alors votre Vm en Powershell on fait : 
On vérifie si l'ont a ssh en taper "ssh" en commande Ensuite
Dans votre powershell ecrivez comme commande -> "ssh root@'IP de la VM'"
Une demande de votre mot de passe root de votre VM va être demandé.
### 3. Firewall

##### A. SSH :

- j'ai modifier mon fichier et utiliser "sudo nano 'votre chemin d'accès du fichier'"
- j'ai modifier en retirant le # à Port 22 sinon c'est considéré comme un commentaire et donc ne sera pas appliqué
- redémarrer ssh pour appliquer les modifications, commande "systemctl restart sshd"
- Vérifier que les modifications ont bien été faits : " ss -l4 -n -p" et donc on à bien :
tcp    LISTEN     0      128                   *:2222                              *:*                   users:(("sshd",pid=3668,fd=3))
- Et donc on ce remet en ssh comme précédemment, et on à bien 
C:\Users\Bo Easy>ssh root@192.168.127.1
ssh: connect to host 192.168.127.1 port 22: Connection refused

- cela ne fonctionne pas tout simplement parceque le SSH n'est plus dans même port, qui est 2222 mainteant et SSH ecoute en 22

- En solution, si l'on à pas eteint son powershell en SSH on remet la VM en port 22, si jamais on l'a eteint on retourne sur la VM en créant un nouveau port, pour cela il va vous falloir utiliser aussi le firewall, on fait "firewall-cmd --add-port=2222/tcp --permanent" et donc ensuite on reload avec "--reload" et donc tu te reconnecte sur le powershell avec "ssh -p 'nouveau port'(içi 2222) root@'IP de la VM' "

#### B. netcat
- j'ai installer netcat avec "yum install nc"
- sur un premier terminal, comme guidé j'ai écris "nc -l 5454" pour créer un server 5454
- sur un deuxieme j'ai connecter netcat "nc" en commande
- sur un dernier terminal j'ai tester si il y avait bien mon server et donc fait un "ss -l4 -n -p" et donc on à bien :
tcp    LISTEN     0      10                    *:5454                              *:*                   users:(("nc",pid=3765,fd=4))

## III. Routage statique

1. Préparation des hôtes
Préparation avec câble
IP PC1 : 192.168.112.1/30
IP PC2 : 192.168.112.2/30
Vérifier que les deux PC se ping :
Préparation VirtualBox
Modifier vos réseaux host-only pour qu'ils soient :

PC1 : réseau 1 : 192.168.101.0/24
PC2 : réseau 2 : 192.168.102.0/24
Créez (ou réutilisez) une VM, et modifiez son adresse :

VM1 (sur PC1) : 192.168.101.10
VM2 (sur PC2) : 192.168.102.10
Check
Assurez vous que :

PC1 et PC2 se ping en utilisant le réseau 12
C:\Users\theod>ping 192.168.112.2

Envoi d’une requête 'Ping'  192.168.112.2 avec 32 octets de données :
Réponse de 192.168.112.2 : octets=32 temps=7 ms TTL=128
Réponse de 192.168.112.2 : octets=32 temps=7 ms TTL=128

Statistiques Ping pour 192.168.112.2:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 7ms, Maximum = 7ms, Moyenne = 7ms
#### Préparation VirtualBox  
PS C:\Users\antoi> ping 192.168.112.1
 
Envoi d’une requête 'Ping'  192.168.112.1 avec 32 octets de données :
Réponse de 192.168.112.1 : octets=32 temps=5 ms TTL=128
Réponse de 192.168.112.1 : octets=32 temps=5 ms TTL=128
 
Statistiques Ping pour 192.168.112.1:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 5ms, Maximum = 5ms, Moyenne = 5ms
VM1 et PC1 se ping en utilisant le réseau 1
VM1 vers PC1 :

[root@localhost ~]# ping 192.168.112.1
PING 192.168.112.1 (192.168.112.1) 56(84) bytes of data.
64 bytes from 192.168.112.1: icmp_seq=1 ttl=127 time=0.728 ms
64 bytes from 192.168.112.1: icmp_seq=2 ttl=127 time=1.25 ms
--- 192.168.112.1 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3002ms
rtt min/avg/max/mdev = 0.728/1.124/1.370/0.245 ms
PC1 vers VM1 :

C:\Users\theod>ping 192.168.101.10

Envoi d’une requête 'Ping'  192.168.101.10 avec 32 octets de données :
Réponse de 192.168.101.10 : octets=32 temps<1ms TTL=64
Réponse de 192.168.101.10 : octets=32 temps<1ms TTL=64

Statistiques Ping pour 192.168.101.10:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 0ms, Maximum = 0ms, Moyenne = 0ms
VM2 et PC2 se ping en utilisant le réseau 2
VM2 vers PC2 :

[root@localhost ~]# ping 192.168.112.2
PING 192.168.112.2 (192.168.112.2) 56(84) bytes of data.
64 bytes from 192.168.112.2: icmp_seq=1 ttl=127 time=1.12 ms
64 bytes from 192.168.112.2: icmp_seq=2 ttl=127 time=1.28 ms
--- 192.168.112.2 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2007ms
rtt min/avg/max/mdev = 1.124/1.217/1.288/0.079 ms
PC2 vers VM2 :

PS C:\Users\antoi> ping 192.168.102.10
 
Envoi d’une requête 'Ping'  192.168.102.10 avec 32 octets de données :
Réponse de 192.168.102.10 : octets=32 temps=5 ms TTL=128
Réponse de 192.168.102.10 : octets=32 temps=4 ms TTL=128
 
Statistiques Ping pour 192.168.102.10:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 4ms, Maximum = 5ms, Moyenne = 4ms
Observez vos tables de routage sur tous les hôtes, pour comprendre :

PC1 :
C:\Users\theod>route print -4
IPv4 Table de routage
===========================================================================
Itinéraires actifs :
Destination réseau    Masque réseau  Adr. passerelle   Adr. interface Métrique
    192.168.101.0    255.255.255.0         On-link     192.168.101.1    281
    192.168.101.1  255.255.255.255         On-link     192.168.101.1    281
  192.168.101.255  255.255.255.255         On-link     192.168.101.1    281
    192.168.112.0  255.255.255.252         On-link     192.168.112.1    281
    192.168.112.1  255.255.255.255         On-link     192.168.112.1    281
    192.168.112.3  255.255.255.255         On-link     192.168.112.1    281
===========================================================================
Itinéraires persistants :
  Aucun
VM1 :
[root@localhost ~]# ip route
default via 10.0.2.2 dev enp0s3 proto dhcp metric 100
10.0.2.0/24 dev enp0s3 proto kernel scope link src 10.0.2.15 metric 100
192.168.101.0/24 dev enp0s8 proto kernel scope link src 192.168.101.10 metric 101
PC2 :
PS C:\Users\antoi> route print -4
IPv4 Table de routage
===========================================================================
Itinéraires actifs :
Destination réseau    Masque réseau  Adr. passerelle   Adr. interface Métrique
    192.168.102.0    255.255.255.0         On-link     192.168.102.2    281
    192.168.102.2  255.255.255.255         On-link     192.168.102.2    281
  192.168.102.255  255.255.255.255         On-link     192.168.102.2    281
    192.168.112.0  255.255.255.252         On-link     192.168.112.2    281
    192.168.112.2  255.255.255.255         On-link     192.168.112.2    281
    192.168.112.3  255.255.255.255         On-link     192.168.112.2    281
===========================================================================
Itinéraires persistants :
  Aucun
VM2 :
[root@localhost ~]# ip route
default via 10.0.2.2 dev enp0s3 proto dhcp metric 100
10.0.2.0/24 dev enp0s3 proto kernel scope link src 10.0.2.15 metric 100
192.168.102.0/24 dev enp0s8 proto kernel scope link src 192.168.102.10 metric 101
Activation du routage sur les PCs
modifier la clé registre HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\ Services\Tcpip\Parameters\IPEnableRouter en passant sa valeur de 0 à 1
activer le service de routage ("lancer l'application de routage")
Win + R
services.msc > Entrée
Naviguer à "Routage et accès distant"
Clic-droit > Propriétés
Pour le démarrage, sélectionner "Automatique"
Appliquer
Démarrer
2. Configuration du routage
Récapitulatif :

PC1 : 192.168.112.1/30
VM : 192.168.101.10/24
PC2 : 192.168.112.2/30
VM2 : 192.168.102.10/24
PC1
PC1 accède déjà aux réseaux 1 et 12, il faut juste lui dire comment accéder au réseau 2
route add 192.168.102.0 mask 255.255.255.0 192.168.112.2
PC1 ping correctement PC2 dans le réseau 2

PC2 vers PC1 dans le réseau 2
route add 192.168.101.0 mask 255.255.255.0 192.168.112.1
PC2 ping correctement PC1 dans le réseau 2

