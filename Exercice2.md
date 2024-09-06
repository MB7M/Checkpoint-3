## Partie 1 : Gestion des utilisateurs

### Q.2.1.1  

```bash
# J'utilise la commande adduser pour créer le compte et le mdp
root@SRVLX01:/home/wilder# adduser mohamed
Ajout de l'utilisateur « mohamed » ...
Ajout du nouveau groupe « mohamed » (1001) ...
Ajout du nouvel utilisateur « mohamed » (1001) avec le groupe « mohamed » ...
Création du répertoire personnel « /home/mohamed »...
Copie des fichiers depuis « /etc/skel »...
Nouveau mot de passe :
Retapez le nouveau mot de passe :
passwd: password updated successfully
Changing the user information for mohamed
Enter the new value, or press ENTER for the default
        Full Name []:
        Room Number []:
        Work Phone []:
        Home Phone []:
        Other []:
Cette information est-elle correcte ? [O/n]O
# vérification de la création :
root@SRVLX01:/home/wilder# su - mohamed
mohamed@SRVLX01:~$
```

### Q.2.1.2  

Tout d'abord, mettre un mdp robuste minimum 12 caractères mélangeant majuscules et miniscules, ensuite restreindre l'accés aux autres utilisateurs au répértoire personnel : 

```bash
mohamed@SRVLX01:~$ chmod 700 /home/mohamed/
mohamed@SRVLX01:~$ ls -ld /home/mohamed/
drwx------ 2 mohamed mohamed 4096  6 sept. 11:54 /home/mohamed/
```

## Partie 2 : Configuration de SSH   

### Q.2.2.1  

Désactiver complètement l'accès à distance de l'utilisateur root.  

en éditant le fichier de config suivant :

```bash
root@SRVLX01:/home/mohamed# nano /etc/ssh/sshd_config
```
J'ai modifier la ligne PermitRootLogin en "no"  
![Description de l'image](https://github.com/MB7M/Checkpoint-3/blob/main/Images/q2.2.2.1.png)  

Puis j'ai redémarrer le service ssh pour appliquer les modifications  

```bash
root@SRVLX01:/home/mohamed# systemctl restart ssh
```

### Q.2.2.2  

en éditant le fichier de config suivant :

```bash
root@SRVLX01:/home/mohamed# nano /etc/ssh/sshd_config
```
j'ai ajouté la ligne AllowUsers mohamed :

![Description de l'image](https://github.com/MB7M/Checkpoint-3/blob/main/Images/q2.2.2..png)

Puis j'ai redémarrer le service ssh pour appliquer les modifications  

```bash
root@SRVLX01:/home/mohamed# systemctl restart ssh
```

### Q.2.2.3  

en éditant le fichier de config suivant :

```bash
root@SRVLX01:/home/mohamed# nano /etc/ssh/sshd_config
```
j'ai modifier les lignes : PasswordAuthentication no, PubkeyAuthentication yes :

![Description de l'image](https://github.com/MB7M/Checkpoint-3/blob/main/Images/q2.2.3.png)

Puis j'ai redémarrer le service ssh pour appliquer les modifications  

```bash
root@SRVLX01:/home/mohamed# systemctl restart ssh
```

## Partie 3 : Analyse du stockage

### Q.2.3.1    

```bash
# Avec la commande df -h pour lister les systèmes de fichiers montés

root@SRVLX01:/home/mohamed# df -h
Sys. de fichiers         Taille Utilisé Dispo Uti% Monté sur
udev                       470M       0  470M   0% /dev
tmpfs                       98M    608K   98M   1% /run
/dev/mapper/cp3--vg-root   2,7G    1,5G  1,1G  60% /
tmpfs                      489M     16K  489M   1% /dev/shm
tmpfs                      5,0M       0  5,0M   0% /run/lock
/dev/md0p1                 471M     49M  398M  11% /boot
tmpfs                       98M       0   98M   0% /run/user/1000
```

### Q.2.3.2  

```bash
# Avec la commande lsblk -f pour lister les systèmes de stockage utilisés

root@SRVLX01:/home/mohamed# lsblk -f
NAME                   FSTYPE            FSVER    LABEL UUID                                   FSAVAIL FSUSE% MOUNTPOINT
sda
└─sda1                 linux_raid_member 1.2      cp3:0 32332561-cf16-c858-7035-17e881dd5c10
  └─md0
    ├─md0p1            ext2              1.0            9bba6d48-3e4b-42a6-bccc-12836de215ec    397,3M    10% /boot
    ├─md0p2
    └─md0p5            LVM2_member       LVM2 001       tlCGJ2-LG5u-kWGc-8kuO-wAiU-icBu-07BEcN
      ├─cp3--vg-root   ext4              1.0            bbc31a37-8e49-47fe-8fad-a3fe18919fdd        1G    56% /
      └─cp3--vg-swap_1 swap              1              8220bf51-2675-4203-91af-1c149f717652                  [SWAP]

# on voit bien que le RAID et le LVM sont utilisés
```

### Q.2.3.3  

Aprés avoir ajouté le nouveau disque dans virtualbox paramétres de la VM : Setting > Storage > Controller : SATA : + :  

![Description de l'image](https://github.com/MB7M/Checkpoint-3/blob/main/Images/q2.3.3.png)

j'ai utilisé la commande lsblk pour l'identifier, il apparait sous sdb :   

```bash
root@SRVLX01:/home/mohamed# lsblk
NAME                   MAJ:MIN RM   SIZE RO TYPE  MOUNTPOINT
sda                      8:0    0     8G  0 disk
└─sda1                   8:1    0     8G  0 part
  └─md0                  9:0    0     8G  0 raid1
    ├─md0p1            259:0    0 488,3M  0 part  /boot
    ├─md0p2            259:1    0     1K  0 part
    └─md0p5            259:2    0   7,5G  0 part
      ├─cp3--vg-root   253:0    0   2,8G  0 lvm   /
      └─cp3--vg-swap_1 253:1    0   976M  0 lvm   [SWAP]
sdb                      8:16   0     8G  0 disk
sr0                     11:0    1  1024M  0 rom
````
pour réparer le volume RAID : 

```bash
# j'ai d'abord vérifier son etat, il y'a qu'un seul disque actif sur sda1, il est censé en avoir deux :

root@SRVLX01:/home/mohamed# mdadm --detail /dev/md0
/dev/md0:
           Version : 1.2
     Creation Time : Tue Dec 20 10:02:28 2022
        Raid Level : raid1
        Array Size : 8381440 (7.99 GiB 8.58 GB)
     Used Dev Size : 8381440 (7.99 GiB 8.58 GB)
      Raid Devices : 2
     Total Devices : 1
       Persistence : Superblock is persistent

       Update Time : Fri Sep  6 12:40:32 2024
             State : clean, degraded
    Active Devices : 1
   Working Devices : 1
    Failed Devices : 0
     Spare Devices : 0

Consistency Policy : resync

              Name : cp3:0
              UUID : 32332561:cf16c858:703517e8:81dd5c10
            Events : 2624

    Number   Major   Minor   RaidDevice State
       0       8        1        0      active sync   /dev/sda1
       -       0        0        1      removed

# J'ai ensuite ajouté le nouveau disque /dev/sdb au RAID
root@SRVLX01:/home/mohamed# mdadm --manage /dev/md0 --add /dev/sdb
mdadm: added /dev/sdb

# J'ai vérifier la reconstruction
root@SRVLX01:/home/mohamed# watch cat /proc/mdstat
Toutes les 2,0s: cat /proc/mdstat                                                                                          SRVLX01: Fri Sep  6 12:46:32 2024

Personalities : [raid1] [linear] [multipath] [raid0] [raid6] [raid5] [raid4] [raid10]
md0 : active raid1 sdb[2] sda1[0]
      8381440 blocks super 1.2 [2/2] [UU]

unused devices: <none>

# Enfin, j'ai vérifié l'état final du RAID qui est actif avec deux disques en miroir

root@SRVLX01:/home/mohamed# mdadm --detail /dev/md0
/dev/md0:
           Version : 1.2
     Creation Time : Tue Dec 20 10:02:28 2022
        Raid Level : raid1
        Array Size : 8381440 (7.99 GiB 8.58 GB)
     Used Dev Size : 8381440 (7.99 GiB 8.58 GB)
      Raid Devices : 2
     Total Devices : 2
       Persistence : Superblock is persistent

       Update Time : Fri Sep  6 12:45:07 2024
             State : clean
    Active Devices : 2
   Working Devices : 2
    Failed Devices : 0
     Spare Devices : 0

Consistency Policy : resync

              Name : cp3:0
              UUID : 32332561:cf16c858:703517e8:81dd5c10
            Events : 2657

    Number   Major   Minor   RaidDevice State
       0       8        1        0      active sync   /dev/sda1
       2       8       16        1      active sync   /dev/sdb
```


### Q.2.3.4  

Tout d'abord je vérifie si j'ai assez de stockage dans mon groupe de volume (free PE environ 3,79) donc suffisant pour le nouveau volume:

```bash
root@SRVLX01:/home/mohamed# vgdisplay
  --- Volume group ---
  VG Name               cp3-vg
  System ID
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  3
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                2
  Open LV               2
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               7,51 GiB
  PE Size               4,00 MiB
  Total PE              1923
  Alloc PE / Size       953 / 3,72 GiB
  Free  PE / Size       970 / <3,79 GiB
  VG UUID               BMardR-vLO6-CToa-ad0f-XVh0-ODeS-cX7Obt

# Ensuite j'ai crée le volume logique "storage" de 2go

root@SRVLX01:/home/mohamed# lvcreate -L 2G -n storage cp3-vg
  Logical volume "storage" created.

# J'ai formaté le volume en ext4

root@SRVLX01:/home/mohamed# mkfs.ext4 /dev/cp3-vg/storage
mke2fs 1.46.2 (28-Feb-2021)
Creating filesystem with 524288 4k blocks and 131072 inodes
Filesystem UUID: ce82e2d8-7be8-4394-8be4-4af3ceeb8614
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912

Allocating group tables: done
Writing inode tables: done
Creating journal (16384 blocks): done
Writing superblocks and filesystem accounting information: done

# J'ai monté le volume dans le répértoire /var/lib/bareos/storage

root@SRVLX01:/home/mohamed# mount /dev/cp3-vg/storage /var/lib/bareos/storage

# J'ai vérifié le montage

root@SRVLX01:/home/mohamed# df -h | grep /var/lib/bareos/storage
/dev/mapper/cp3--vg-storage   2,0G     24K  1,8G   1% /var/lib/bareos/storage

# pour le monter automatiquement à chaque démarrage, j'ai commencé par obtenir son UUID

root@SRVLX01:/home/mohamed# blkid /dev/cp3-vg/storage
/dev/cp3-vg/storage: UUID="ce82e2d8-7be8-4394-8be4-4af3ceeb8614" BLOCK_SIZE="4096" TYPE="ext4"

# ensuite j'ai modifier le fichier /etc/fstab en y ajoutant la ligne :

UUID=ce82e2d8-7be8-4394-8be4-4af3ceeb8614  /var/lib/bareos/storage  ext4  defaults  0  2
# pour tester le bon fonctionnement du montage automatique, j'ai monté et démonter le volume et vérifier avec df -h

root@SRVLX01:/home/mohamed# umount /var/lib/bareos/storage
root@SRVLX01:/home/mohamed# mount -a
root@SRVLX01:/home/mohamed# df -h | grep /var/lib/bareos/storage
/dev/mapper/cp3--vg-storage   2,0G     24K  1,8G   1% /var/lib/bareos/storage
```

### Q.2.3.5   

il reste maintenant 1,79 go d'espace disponible :

```bash
root@SRVLX01:/home/mohamed# vgdisplay cp3-vg
  --- Volume group ---
  VG Name               cp3-vg
  System ID
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  4
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                3
  Open LV               3
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               7,51 GiB
  PE Size               4,00 MiB
  Total PE              1923
  Alloc PE / Size       1465 / 5,72 GiB
  Free  PE / Size       458 / <1,79 GiB
  VG UUID               BMardR-vLO6-CToa-ad0f-XVh0-ODeS-cX7Obt
```

---

## Partie 4 : Sauvegardes  

### Q.2.4.1  

- **Bareos Director :** Planifie et contrôle les tâches de sauvegarde.
- **Bareos File Daemon (FD) :** Installé sur les machines à sauvegarder, collecte les données.
- **Bareos Storage Daemon (SD) :** Gère l'écriture des sauvegardes sur les supports de stockage.

## Partie 5 : Filtrage et analyse réseau  

### Q.2.5.1   

```bash
root@SRVLX01:/home/mohamed# nft list ruleset
table inet inet_filter_table {
        chain in_chain {
                type filter hook input priority filter; policy drop;
                ct state established,related accept
                ct state invalid drop
                iifname "lo" accept
                tcp dport 22 accept
                ip protocol icmp accept
                ip6 nexthdr ipv6-icmp accept
        }
}

```

On peut constater d'aprés la sortie de commande les régles appliquées :

1. Connexions établies/associées : Les paquets appartenant à des connexions déjà établies ou liées sont acceptés
2. Paquets invalides : Les paquets invalides sont rejetés
3. Interface loopback : Le trafic sur l'interface locale (loopback) est accepté
4. SSH (port 22) : Les connexions SSH sur le port 22 sont autorisées
5. ICMP (IPv4 et IPv6) : Les paquets ICMP pour IPv4 et IPv6 (ping et diagnostics) sont acceptés
 
### Q.2.5.2  

Quels types de communications sont autorisées ?   

- Connexions établies et liées (established, related)
- Trafic SSH sur le port 22 (permettant les connexions SSH entrantes)
- Trafic ICMP 4 et 6
- Trafic local via l'interface loopback (`lo`).

### Q.2.5.3  

Quels types sont interdites ?  

- Nouvelles connexions non autorisées (toutes les nouvelles connexions sauf SSH).
- Trafic vers des ports non explicitement autorisés
- Paquets invalides sont automatiquement rejetés. (state invalid drop)
- Tous les autres paquets sont **rejetés** par la politique par défaut. (policy drop)

### Q.2.5.4  

J'ai utilisé la commande suivante pour permettre les connexions depuis mon réseau local 172.20.0.0/24 sur les ports 9101 à 9103 :  

```bash
root@SRVLX01:/home/mohamed# nft add rule inet inet_filter_table in_chain ip saddr 172.20.0.0/24 tcp dport 9101-9103 accept

# puis j'ai vérifier l'ajout de la régle

root@SRVLX01:/home/mohamed# nft list ruleset
table inet inet_filter_table {
        chain in_chain {
                type filter hook input priority filter; policy drop;
                ct state established,related accept
                ct state invalid drop
                iifname "lo" accept
                tcp dport 22 accept
                ip protocol icmp accept
                ip6 nexthdr ipv6-icmp accept
                ip saddr 172.20.0.0/24 tcp dport 9101-9103 accept
        }
}
```

## Partie 6 : Analyse de logs  

### Q.2.6.1  

J'ai consulté les logs du dossier /var/log/auth.log pour filtrer à travers grep les derniers echecs de connexion sur le serveur, il en resort 3 :

```bash
root@SRVLX01:/home/mohamed# grep "authentication failure" /var/log/auth.log | tail -n 10
Sep  6 11:45:06 SRVLX01 su: pam_unix(su:auth): authentication failure; logname=wilder uid=1000 euid=0 tty=tty1 ruser=wilder rhost=  user=root
Sep  6 12:05:19 SRVLX01 su: pam_unix(su:auth): authentication failure; logname=wilder uid=1001 euid=0 tty=pts/0 ruser=wilder rhost=  user=root
Sep  6 12:36:43 SRVLX01 sshd[1231]: pam_unix(sshd:auth): authentication failure; logname= uid=0 euid=0 tty=ssh ruser= rhost=10.10.10.6  user=wilder
````
Analyse :   

1. Date et heure : Sep 6 11:45:06  
   Adresse IP : Local (connexion via `tty`)  
   Utilisateur : root  
   Service : su (tentative de devenir root depuis un terminal local)

2. Date et heure : Sep 6 12:05:19  
   Adresse IP : Local (connexion via `pts/0`)  
   Utilisateur : root  
   Service : su (tentative de devenir root depuis un terminal local)

3. Date et heure : Sep 6 12:36:43  
   Adresse IP : 10.10.10.6  
   Utilisateur : wilder  
   Service : sshd (tentative de connexion SSH)
