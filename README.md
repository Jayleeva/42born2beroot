Projet du 2ème cercle du cursus 42

# Consignes:
**Mettre en place un serveur en suivant des règles spécifiques.**

## Partie obligatoire
Utiliser VirtualBox, avec Debian comme OS. SElinux et Apparmor doivent rester actifs.

Deux partitions chiffrées doivent être créées avec LVM. Un service SSH doit être actif sur le port 4242 (qui doit être le seul port ouvert); l'utilisateur root ne doit pas pouvoir se connecter via SSH.

L'OS doit être configurée avec le pare-feu UFW.

Le hostname = your_login42.

Politique de mot de passe fort: 
- mot de passe expire tous les 30 jours: au moins 7 nouveaux char comparé à l'ancien, sauf pour l'utilisateur root.
- min. 2 jours entre chaque changement de mots de passe
- avertissement 7 jours avant la limite pour changer le mot de passe
- min. 10 char dont 1 maj, 1 min, 1 chiffre et pas plus de 3 rep consécutives d'un même char. Ne doit pas être ou contenir le nom d'utilisateur.

Installation et configuration stricte de sudo:
- 3 essais max pour s'identifier en sudo
- mettre un message d'erreur en cas de mauvais mot de passe sudo
- archiver chaque utilisation de sudo (input et output), dans /var/log/sudo/
- mode TTY activé
- restreindre les paths utilisables par sudo

Un utilisateur en plus de l'utilisateur root; il appartient aux groupes sudo et users42.

Attention, une fois tout configuré, il faudra changer tous les mots de passe.

Un bash script écrira les infos suivantes toutes les 10 min sur tous les terminaux, sans aucune erreur visible:
- architecture de l'OS + sa version kernel
- nombre de processeurs physiques
- nombre de processeurs virtuels
- mémoire vive dispo et % d'utilisation
- mémoire dispo et % d'utilisation
- % d'utilisation des processeurs
- date et heure du dernier redémarrage
- statut de LVM (actif ou pas)
- nombre de connexions actives
- nombre d'utilisateurs
- adresse IPv4 et adresse MAC
- nombre de commandes effectuées avec sudo

# Notions importantes
- Virtualbox = logiciel pour créer des machines virtuelles
- Debian = une des OS pour linux.
- SElinux = Security Enhanced Linux
- Apparmor = gère les privilèges pour limiter les accès des applications, est actif par défaut.
- partition chiffrée = séparation du disque pour stocker les choses à des endroits séparés + encryption. 
- LVM (Logical Volumes Manager) = sert à gérer les volumes logiques.
- service SSH = service d'identification et de transfert sécurisé des données (encryption).
- pare-feu UFW (Uncomplicated FireWall) = service en plus du SSH qui vérifie que la connexion aux ports est autorisée. 
- hostname = your_login42. 
- sudo = utilisé pour forcer des commandes autrement pas autorisées. Configurable
- mode TTY = sert à vérifier qu'on est dans un terminal. Utilisé dans le fichier sudoers avec ``requiretty`` : toutes les commandes sudo ne peuvent se faire que dans un terminal. 
- utilisateur root = administrateur
- adresse IPv4 = permet de localiser la machine.
- adresse MAC (Mandatory Adress Control) = utilisé par APParmor pour identifier qui fait quoi avec quel appli.
- cron (chrono tab -> "table de planification") = programme qui permet aux utilisateurs des systèmes Unix d’exécuter automatiquement des scripts, des commandes ou des logiciels à une date et une heure spécifiée à l’avance, ou selon un cycle défini à l’avance.

# Création de la machine
Normalement, Visualbox est déjà installé sur les Mac de l'école, mais n'est possiblement pas à jour: d'une version à l'autre, l'interface ne sera pas la même et les étapes différeront légèrement.

1. Télécharger l'image Debian amd64 via ce site: https://www.debian.org/distrib/netinst.
2. Lancer Visualbox, cliquer sur "new".
3. Choisir le nom et le path de la machine (dans le goinfre ou sur un disque externe pour avoir assez de place), choisir Linux et la version Debian. En fonction de votre version, donner le path de l'image.
4. En fonction de la version: choisir la taille de la RAM (au moins 4GB) et le nombre de processeurs (au moins 2) que vous souhaitez allouer à votre VM. Dans tous les cas, sélectionner "Create a virtual hard disk now" (VDI, dynamically allocated) et allouer au minimum 10GB de disque pour être sûr d'avoir assez d'espace pour l'exercice. Si votre version vous le propose, cocher "pre-allocate full size".
5. En fonction de votre version, cliquer sur votre machine > settings > storage > optical drive et donner le path de l'image.

# Installation
1. A l'aide des flèches du clavier, choisir l'option "install". **ATTENTION: cela ne suffit pas pour répondre au critère "pas d'interface graphique" selon certains évaluateurs.** Pour les satisfaire, ne pas oublier la manipulation :[].
2. Choisir la langue.
3. Choisir le pays.
4. Choisir le clavier.
5. Entrer votre hostname (your_login42).
6. Enter (pas besoin de nom de domaine).
7. Créer un mot de passe root (à noter quelque part).
8. Créer un premier utilisateur (your_first_name your_last_name par ex).
9. Créer un mot de passe pour le premier utilisateur (à noter quelque part).
10. Choisir la timezone.
11. Si vous voulez faire vite et simple: choisir "Guided - use entire disk and set up encrypted LVM". Si vous voulez fouiller: fouiller. 
12. Enter (il n'y a qu'un seul disque à partitionner).
13. Si vous voulez faire vite et simple: choisir "Separate /home, /var, and /tmp partitions". Si vous voulez fouiller: fouiller.
14. Yes > Enter (nous voulons enregistrer les modifications et configuer les volumes logiques).
15. Choisir une phrase d'encryption (à noter quelque part).
16. A la question "Amount of volume group to use for guided partitionning", entrer "max".
17. Si vous aviez décider de fouiller au lieu de prendre l'option vite et simple, c'est ici que vous allez devoir créer vos partitions. Une fois fini, choisir "Finish partitionning and write changes to disk", confirmer.
18. No > Enter (nous n'avons pas d'autre disque à scanner).
19. Choisir un miroir : prendre le pays le plus proche du sien.
20. Choisir deb.debian.org
21. Enter (pas besoin de proxy).
22. No > Enter (nous ne voulons pas participer au sondage).
23. Sélectionner ou non (avec espace) "SSH Server" et "standard system utilities", c'est un peu égal visiblement.
24. Yes > Enter (nous voulons installer GRUB).
25. Choisir le disque /dev/sda...
26. Continue > Enter (on a fini).

# Commandes importantes pour la configuration
``su -`` : permet de se log en tant que superutilisateur (root)

``apt-get update -y`` et ``apt-get upgrade -y`` : permettent de vérifier et mettre à jour les paquets de Debian?

## Sudo
``apt install sudo`` : installe sudo

``usermod -aG sudo your_username`` : ajoute l'utilisateur dans le groupe sudo.

``getent group sudo`` : affiche les membres du groupe sudo.

``sudo visudo`` : ouvre le fichier sudoers (privilèges des membres du groupe?) lire man pour sudoers file. Enregistrer avec ctrl + o et quitter avec ctrl + x.

## Service SSH et UFW
``sudo apt install openssh-server`` : installe le service ssh

``apt install vim`` : installe vim

``sudo vim /etc/ssh/sshd_config`` : ouvre le fichier sshd_config, où on peut changer le port.

``sudo grep Port /etc/ssh/sshd_config`` : affiche le port (vérifier si bien changé).

``sudo service ssh restart`` : redémarre le service ssh.

``apt-get install ufw`` : installe le pare-feu UFW.

``sudo ufw enable`` : enclenche le pare-feu.

``sudo ufw allow ssh`` : écrit une nouvelle règle qui autorise ssh dans le pare-feu?

``sudo ufw allow 4242``: écrit une nouvelle règle qui autorise le port 4242 dans le pare-feu?

``ssh <username>@127.0.0.1 -p 4242`` : permet de se connecter à la machine virtuelle depuis un terminal de l'hôte.

Depuis VirtualBox, cliquer sur votre machine > settings > network > ports redirection > add new rule, mettre "4242" dans le port hôte et le port invité.

## Politique de mots de passe
``sudo apt-get install libpam-pwquality`` : installe la librairie pour instaurer une politique de mots de passe.

``sudo vim /etc/pam.d/common-password`` : ouvre le fichier common-password, où on peut changer la politique de mots de passe. A la ligne pam_pwquality.so, après ``retry=3``, ajouter ``minlen=10 ucredit=-1 lcredit=-1 dcredit=-1 maxrepeat=3 reject_username difok=7 enforce_for_root`` (min. 10 char, dont au moins 1 uppercase 1 lowercase et 1 digit, 3 mêmes char à suivre max, au moins 7 char différents de l'ancien mot de passe quand changement, sauf pour root: on ne lui demande pas l'ancien au moment de changer donc pas besoin de comparer).

ET / OU?
``/etc/security/pwquality.conf`` : ouvre le fichier pwquality.conf, où on peut aussi changer la politique de mots de passe. 

``sudo vim /etc/login.defs`` : ouvre le fichier login.defs, où on peut changer la politique de changement de mots de passe. Trouver ``PASS_MAX_DAYS 9999 PASS_MIN_DAYS 0 PASS_WARN_AGE 7`` et changer en ``PASS_MAX_DAYS 30 PASS_MIN_DAYS 2 PASS_WARN_AGE 7`` (doit changer tous les 30 jours, peut pas être changé avant 2 jours, utilisateur prévenu 7 jours avant le changement).

ATTENTION, ne se met pas à jour chez les utilisateurs qui étaient déjà créées. Il faut entrer les commandes:
- ``sudo chage -M 30 <username/root>`` change le max de jours avant le changement de mot de passe, pour l'utilisateur nommé.

- ``sudo chage -m 2 <username/root>`` change le min de jours avant le changement de mot de passe, pour l'utilisateur nommé.

- ``sudo chage -W 7 <username/root>`` change le warning avant le changement de mot de passe, pour l'utilisateur nommé.

``chage -l username`` : affiche la politique de l'utilisateur nommé.

## Groupes
``sudo groupadd user42`` : crée le groupe user42.

``getent group`` : affiche les groupes existants.

``cut -d: -f1 /etc/passwd`` : affiche les utilisateurs locaux.

``sudo adduser new_username`` : crée un utilisateur.

``sudo userdel -r username`` : supprimer un utilisateur et tout ce qui lui est lié.

``sudo usermod -aG user42 your_username`` : ajoute l'utilisateur nommé (ici your_username) dans le groupe nommé (ici user42).

## Cron
cron (chrono tab -> "table de planification") est un programme qui permet aux utilisateurs des systèmes Unix d’exécuter automatiquement des scripts, des commandes ou des logiciels à une date et une heure spécifiée à l’avance, ou selon un cycle défini à l’avance.

ouvrir le fichier avec ``sudo crontab -e``
ajouter:
``*/10 * * * * /usr/local/bin/monitoring.sh``
Pour exécuter le bash toutes les 10 minutes (si le bash est appelé différemment ou enregistré ailleurs, adapter le path).

## hostname
changer hostname avec commande ``sudo hostnamectl set-hostname your_username42``

PUIS

``nano /etc/hosts`` changer hostname.

## bash
Le texte à afficher appelle les variables ``"Info: $var_name"`` qui elles appellent des lignes de commande ``var_name=$(command1 -flag | command2)``.

Certaines données qu'on doit afficher pour l'exercice sont accessibles facilement à l'aide d'une commande directe (comme l'adresse IP par ex, qui se récupère d'un flag après la commande ``hostname``). D'autres sont récupérables dans des fichiers qu'il faut un peu fouiller (ex.: le pourcentage de CPU utilisé qui s'obtient en additionnant l'utilisation du système et celle de l'utilisateur), voire doivent se calculer sur la base de données dispersées dans des fichiers (ex.: le pourcentage d'espace disque utilisé). Je vous recommande de faire vos propres tests et fouilles pour être mieux armé-e pendant l'évaluation.

La commande ``awk`` sert à accéder à une variable du fichier ouvert, on l'utilise à chaque fois qu'on veut imprimer une donnée stockée telle quelle dans une colonne par ex.

La commande ``wc`` sert à compter le nombre d'éléments (lignes avec le flag ``-l``, mots avec le flag ``-w``) du fichier ouvert; on peut préciser des lignes avec la commande ``grep``.


### Détails du bash

``#! /bin/bash`` : déclare le fichier comme un bash.

``arc=$(uname -a)`` : va chercher tous les noms unix : architecture, machine, ..., ..., OS, ..., ...

``pcpu=$(grep "physical id" /proc/cpuinfo | uniq | wc -l)`` : va chercher et compte toutes les lignes uniques qui contiennent "physical id" dans le fichier cpuinfo. Permet de compter le nombre de CPU physiques.

``vcpu=$(grep "^processor" /proc/cpuinfo | wc -l)`` : va chercher et compte toutes les lignes qui commencent par "processor" dans le fichier cpuinfo. Permet de compter le nombre de vCPU.

``fram=$(free -m | awk '$1 == "Mem:" \{print $2\}')\`` : va chercher dans les informations sur la RAM, la ligne où la colonne 1 s'appelle "Mem:", imprime le contenu de la colonne 2, soit l'espace ram encore libre.

``uram=$(free -m | awk '$1 == "Mem:" \{print $3\}')\`` : va chercher dans les informations sur la RAM, la ligne où la colonne 1 s'appelle "Mem:", imprime le contenu de la colonne 3, soit l'espace ram utilisé.

``pram=$(free | awk '$1 == "Mem:" \{printf("%.2f"), $3/$2*100\}')\`` : va chercher dans les informations sur la RAM, la ligne où la colonne 1 s'appelle "Mem:", imprime en format float avec 2 décimales le résultat du calcul du pourcentage [part utilisée sur part libre (?)].

``fdisk=$(df -BG | grep '^/dev/' | grep -v '/boot$' | awk '\{ft += $2\} END \{print ft\}')\`` : va chercher dans les informations sur le disque (affichées en blocs avec l'unité giga), les lignes qui commencent par /dev et qui ne contiennent pas /boot$, cumule toutes les colonnes 2, et à la fin de ce calcul, en imprime le resultat, soit l'espace disque libre. Il n'y a en réalite qu'une seule colonne 2, mais le calcul est laissé car il permet d'éviter des erreurs d'affichage.

``udisk=$(df -BM | grep '^/dev/' | grep -v '/boot$' | awk '\{ut += $3\} END \{print ut\}')\`` : va chercher dans les informations sur le disque (affichées en blocs avec l'unité mega), les lignes qui commencent par /dev et qui ne contiennent pas /boot$, cumule toutes les colonnes 3, et à la fin de ce calcul, en imprime le resultat, soit l'espace disque utilisé. Il n'y a en réalite qu'une seule colonne 3, mais le calcul est laissé car il permet d'éviter des erreurs d'affichage.

``pdisk=$(df -BM | grep '^/dev/' | grep -v '/boot$' | awk '\{ut += $3\} \{ft+= $2\} END \{printf("%d"), ut/ft*100\}')\`` : va chercher dans les informations sur le disque (affichées en blocs avec l'unité mega), les lignes qui commencent par /dev et qui ne contiennent pas /boot$, cumule toutes les colonnes 3 et toutes les colonnes 2 (séparement) et calcule le pourcentage [part utilisée sur part libre (?)]. 

``cpul=$(top -bn1 | grep '^%Cpu' | awk '\{printf("%.1f%%"), $1 + $3\}')\`` : va chercher dans le gestionnaire de tâches la ligne qui commence par %Cpu et imprime en format float avec 1 décimale le resultat de l'addition de la colonne 1 et 3 (système et utilisateur).

``lb=$(who -b | awk '$1 == "system" \{print $3 " " $4\}')\`` : va chercher les infos sur les utilisateurs actuellement connectés en précisant l'heure du dernier system boot. La colonne 3 correspond à la date et la colonne 4 à l'heure.

``lvmu=$(if [ $(lsblk | grep "lvm" | wc -l) -eq 0 ]; then echo no; else echo yes; fi)\`` : va chercher les infos sur les partitions, les lignes contenant "lvm" et les compte, si le résultat est 0 (= LVM n'est pas activé), écrit non; sinon écrit oui; fin de la condition.

``ctcp=$(ss -Ht state established | wc -l)\`` : va chercher dans les infos sur le ssh les connexions en état "établies" et compte les lignes.

``ulog=$(users | wc -w)\`` : va chercher les utilisateurs actuellement connectés et compte le nombre de mots (noms).

``ip=$(hostname -I)\`` : va chercher l'adresse IP dans les informations sur le hostname.

``mac=$(ip link show | grep "ether" | awk '\{print $2\}')\`` : va chercher les infos sur l'adresse IP, la ligne contenant "ether" et imprime la colonne 2, soit l'adresse MAC.

``cmds=$(journalctl _COMM=sudo | grep COMMAND | wc -l)\`` : va chercher et compte le nombre de commandes sudo dans le journal en ignorant les infos superflues (messages et avertissements).

````
wall "	#Architecture: $arc\
	#CPU physical: $pcpu\
	#vCPU: $vcpu\
	#Memory Usage: $uram/$\{fram\}MB ($pram%)\
	#Disk Usage: $udisk/$\{fdisk\}Gb ($pdisk%)\
	#CPU load: $cpul\
	#Last boot: $lb\
	#LVM use: $lvmu\
	#Connections TCP: $ctcp ESTABLISHED\
	#User log: $ulog\
	#Network: IP $ip ($mac)\
	#Sudo: $cmds cmd"\
````
``wall`` permet de faire afficher le message sur tous les terminaux connectés à la machine. La suite est de la mise en page.
