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

