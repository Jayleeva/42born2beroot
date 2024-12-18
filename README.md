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
- Apparmor = gère les privilèges des groupes, est actif par défaut.
- partition chiffrée = séparation du disque pour stocker les choses à des endroits séparés + encryption. 
- LVM (Logical Volumes Manager) = sert à gérer les volumes logiques.
- service SSH = service d'identification et de transfert sécurisé des données (encryption).
- pare-feu UFW (Uncomplicated FireWall) = service en plus du SSH qui vérifie que la connexion aux ports est autorisée. 
- hostname = your_login42. 
- sudo = utilisé pour forcer des commandes autrement pas autorisées. Configurable
- mode TTY = sert à vérifier qu'on est dans un terminal. Utilisé dans le fichier sudoers avec ``requiretty`` : toutes les commandes sudo ne peuvent se faire que dans un terminal. 
- utilisateur root = 
- adresse IPv4 =
- adresse MAC (Mandatory Adress Control) = 

# Commandes importantes
``lsblk`` : affiche les partitions

``su -`` : permet de se log en tant que superutilisateur (root)

``apt-get update -y`` et ``apt-get upgrade -y`` : permettent de vérifier et mettre à jour (?)

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

``sudo ufw status numbered`` : affiche le statut du pare-feu, avec les éléments autorisés.

``sudo systemctl status ssh`` : affiche le statut du service ssh.

``ssh <username>@127.0.0.1 -p 4242`` : permet de se connecter à la machine virtuelle depuis un terminal de l'hôte.

## Politique de mots de passe
``sudo apt-get install libpam-pwquality`` : installe la librairie pour instaurer une politique de mots de passe?

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

``sudo usermod -aG user42 your_username`` : ajoute l'utilisateur nommé dans le groupe nommé (ici user42).

## Cron
cron (chrono tab -> "table de planification") est un programme qui permet aux utilisateurs des systèmes Unix d’exécuter automatiquement des scripts, des commandes ou des logiciels à une date et une heure spécifiée à l’avance, ou selon un cycle défini à l’avance.

ouvrir le fichier avec ``sudo crontab -e``
ajouter:
*/10 * * * * /usr/local/bin/monitoring.sh


## hostname
changer hostname avec commande ``sudo hostnamectl set-hostname your_username42``

PUIS

``nano /etc/hosts`` changer hostname.

## bash
``#! /bin/bash`` : déclare le fichier comme un bash.

``cmd=$(uname -a)`` : va chercher tous les noms unix : architecture, machine, ..., ..., OS, ...,...

``cmd=$(grep "physical id" /proc/cpuinfo | uniq | wc -l)`` : va chercher et compte toutes les lignes uniques qui contiennent "physical id". Permet de compter le nombre de CPU physiques.

``cmd=$(grep "^processor" /proc/cpuinfo | wc -l)`` : va chercher et compte toutes les lignes qui commencent par ^processor. Permet de compter le nombre de vCPU.


``cmd1=$(free --mega -t | grep Total | awk '{ print $3 }')`` : va chercher l'espace mémoire total en megabytes de la colonne 3 (utilisé) de la ligne contenant "Total".

``cmd2=$(free --mega -t | grep Total | awk '{ print $2 }')`` idem mais colonne 2 (total).

``cmd3=$(free --mega -t | grep Total | awk '{ print $3/$2 * 100 }')`` : calcule le pourcentage utilisé.


``cmd3=$(df --block-size=G --total | grep total | awk '{ print $5 }')`` : va chercher l'espace disponible sur le disque (colonne 5 = pourcentage).

```
cmd1=$(df --block-size=G --total | grep total | awk '{ print $3 }')
cmd2=$(df --block-size=G --total | grep total | awk '{ print $2 }')
cmd=$(mpstat | grep all | awk '{print $3+$5}')
```
: va chercher la charge des CPU? 

``cmd=$(who -b | awk '{ print $3 " " $4 }')`` : va chercher les infos sur les utilisateurs actuellement connectés en précisant l'heure du dernier system boot (?)

```
if [ $cmd -gt 0 ]
then
```
: permet d'afficher oui ou non selon si LVM est utilisé ou pas (?)

``cmd=$(cat /etc/fstab | grep /dev/mapper | wc -l)`` : va chercher l'utilisation du LVM.

``cmd=$(echo "$(ss -t state established | wc -l) - 1" | bc)`` : va chercher et compte toutes les lignes où une connexion TCP a été établie (?)

``cmd=$(who | wc -l)`` : va chercher et compte les utilisateurs actuellement connectés.

```
cmd1=$(ip addr | grep enp | grep inet | awk '{print $2}' | cut -d / -f1)
cmd2=$(ip addr | grep ether | awk '{print $2}')
```
: va chercher l'adresse IP (mot-clé "enp" et "inet", colonne 2, jusqu'au délimitateur /, 1er bloc) et MAC (mot-clé "ether", colonne 2).

``cmd=$(journalctl -q | grep COMMAND | wc -l)`` : va chercher et compte le nombre de commandes sudo dans le journal en supprimant toutes les infos superflues (messages et avertissements).

