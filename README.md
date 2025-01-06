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
Pour exécuter le bash toutes les 10 minutes.

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


# Evaluation
Faire une copie / snapshot de la machine.
Pour vérifier plus vite si le script se lance bien après 10 min, lancer la machine, mettre la passphrase, se connecter. Montrer le mot de passe pour vérifier qu'il remplisse bien les critères. 

## Signature
Sur un terminal non connecté à la machine virtuelle, taper ``shasum machine_path.vdi``, copier le résultat dans un fichier test enregistré au même endroit que la signature mise sur git, faire la commande ``diff test.txt signature.txt`` . Si retourne rien, OK.

## Questions générales
### Fonctionnement machine
Ordinateur alloue des ressources (mémoire, processeurs, ...) pour créer et alimenter un nouvel ordinateur à l'intérieur de lui-même. L'hyperviseur s'assure que les deux (ou plus) environnements soient séparés.

### Pourquoi Linux
Contrairement à Windows et Mac, Linux est open source et permet plus de manipulations.
### Différences basiques Rocky et Debian
Debian plus simple pour les débutants, Rocky offre plus de possibilités mais est plus compliqué à prendre en main, il est préféré par les entreprises. Debian est très stable, a une grande communauté et donc une documentation fournie. Sur Rocky, on utilise SElinux, et sur Debian, APParmor.
### Intérêt des VM
Environnement séparé de l'ordi de base, permet de tester des choses, si la VM casse, on la supprime et notre ordi est sauf. Aussi, comme 2 (voire plus) ordis en un, économies d'argent et d'énergie. Peut permettre de tester une autre OS sans racheter un nouvel ordi.
### Différence aptitude et apt
Aptitude va deviner des choses et prendre des initiatives: moins de prise de tête, mais aussi moins de contrôle. Apt fait strictement ce qu'on lui demande de faire et rien d'autre.
### APParmor c'est quoi
Gestionnaire des privilèges des groupes, limite et surveille qui utilise quelle appli de quelle manière. Est actif par défaut sur Debian.

## Pas d'interface graphique?
1. La machine consiste simplement en un terminal.
2. Pour les pinailleurs: taper ``ls /usr/bin/*session`` , si ``usr/bin/dbus-run-session `` s'affiche, ok.

## Bien sur Debian?
C'est écrit partout au lancement, mais si vraiment: ``uname -a``.
## UFW lancé?
Taper ``sudo ufw status``
## SSH lancé?
Taper ``sudo systemctl status ssh``.

## Utilisateur autre que root bien dans sudo et user42?
Taper ``groups``.

## Politique de mots de passe bien config?
J'ai du installer une librairie avec ``apt-get install libpam-pwquality``.

- Ouvrir fichier ``/etc/pam.d/common-password`` --> changé prérequis en ajoutant minlen ucredit lcredit dcredit maxrepeat rejectusername difok enforce_for_root. Difok ne s'applique pas à root car quand on veut changer son mot de passe, l'ancien n'est pas demandé.
- Ouvrir fichier ``/etc/login.defs`` --> changé jours max min et warning
- Taper ``chage -l username`` pour vérifier que bien appliqué aux utilisateurs qui existaient avant le changement de règles. --> changé manuellement pour user et root avec ``chage -M 30`` et ``chage -m 2``.

## Créer nouveau user, vérifier
- Taper ``sudo adduser evaluator``
- Entrer un mot de passe qui ne respecte pas la pol pour voir, puis qui respecte la pol et dont on se souvient facilement, ex: 123qwerty_TEST. Parfois accepte un mot de passe qui ne respecte pas tout, mais empêchera la connexion au ssh ensuite. Pour modifier le mot de passe: ``sudo password username``.
- Réentrer le mot de passe
- entrer blank blank blank etc puis oui.
- Taper ``cut -d: -f1 /etc/passwd`` pour vérifier.

## Créer nouveau groupe et y ajouter nouveau user, vérifier
- Taper ``sudo groupadd evaluating``
- Taper ``sudo usermod -aG evaluating evaluator``
- Taper ``getent group``

## Intérêt, avantages et inconvénients de la politique de mot de passe
Plus sécurisée, mais plus pénible pour l'utilisateur car plus difficile de s'en rappeler + il faut trouver l'inspi tous les 30 jours. Plus c'est pénible pour l'utilisateur, plus c'est difficile pour un hacker, mais il faut trouver un juste milieu.

## Hostname bien formaté?
Taper ``hostname``.

## Renommer le hostname, vérifier, rechanger
- Taper ``sudo hostnamectl set_hostname newname``
- Ouvrir le fichier ``/etc/hosts`` et modifier aussi ici.
- Taper ``sudo reboot``.
- Recommencer.

## Afficher les partitions, expliquer fonctionnement et intérêt
Taper ``lsblk``.

Chaque ``sda`` est une partition: un espace de mémoire réservé pour un truc précis. Le 'a' de 'sda' indique que la partition est dans le disque 'a'; s'il y avait un 'sdb' en plus par ex, cela voudrait dire qu'il y aurait 2 disques. ``sda5_crypt`` est une version chiffrée (encryptée) de ``sda5``. LVM (logical volume manager) permet de faire des sous-partitions en divisant les partitions en volumes logiques.

L'intérêt est qu'on évite les débordements: je m'assure que le système de base ait tout l'espace dont il a besoin pour fonctionner peu importe combien de jeux j'installe sur mon ordi par ex, mais aussi, je peux mettre un truc potentiellement dangereux dans une partition ou un volume spécifique et il n'influera pas sur le reste. LVM donne aussi plus de flexibilité pour organiser ma mémoire, en allant au-delà de la simple séparation des disques.

## Sudo bien installé?
Vu qu'on l'a déjà utilisé plusieurs fois, oui. Mais si vraiment, taper ``dpkg -l | grep sudo``. 

## Assigner nouvel utilisateur à sudo
Taper ``sudo usermod -aG sudo evaluator``.

## Intérêt et fonctionnement de sudo
Donne des passe-droits, on peut forcer des commandes autrement interdites. Permet des manipulations plus poussées. Exemples: faire reboot la machine, modifier des fichiers sur la politique de mots de passe, ajouter des utilisateurs, etc.

## Mise en place des règles sudo demandées
Ouvrir le fichier /etc/sudoers.tmp avec ``sudo visduo``. Regarder les Defaults: max 3 essais de mots de passe, un message d'erreur, le chemin pour le logfile, précision de prendre les input et les output, obligation de TTY (de passer par un terminal), chemins sécurisés (inaccessibles par sudo.)

## Démonstration des règles
- Taper ``su -`` ?
- Entrer un mauvais mot de passe?

## Fichier log existe? se met à jour?
- Taper ``cat /var/log/sudo/sudo.log``
- Quitter, lancer une commande sudo (ex. ``sudo ufw status ``), refaire la commande précédente, comparer.

  ``sudo sudoreplay -l`` TSID0000...

## UFW installé et fonctionne? bien une règle 4242?
- Taper ``dpkg -l | grep ufw``
- Taper ``sudo ufw status``.

## UFW c'est quoi + intérêt?
Pare-feu, limite strictement quel port peut lancer ou recevoir des informations. C'est une sécurité en plus.

## Ajouter port 8080 et vérifier, puis supprimer
- Taper ``sudo ufw allow 8080``
- Taper ``sudo ufw status numbered``.
- Taper ``sudo ufw delete 8080num``.

## SSH installé et fonctionne? que le port 4242? root ne peut pas se connecter? nouvel utilisateur oui?
- Taper ``dpkg -l | grep ssh`` ou ``sudo systemctl status ssh``
- Regarder dans visualbox settings network advances port...
- Ouvrir un nouveau terminal pas dans la machine, taper ``ssh root@localhost -p 4242`` , doit constamment dire que le mot de passe est faux et arrêter après 3 essais.
- Dans ce nouveau terminal, taper ``ssh evaluator@localhost -p 4242``, doit pouvoir se connecter avec le mot de passe choisi à la création du compte.

## Expliquer script
Taper ``sudo nano /usr/local/bin/monitoring.sh``
Stimuler le CPUl pour voir des chiffres au lieu du 0.0% : ``sha1sum /dev/zero``

## Script se lance toutes les 10 min
Taper ``sudo crontab -e`` pour voir la ligne qui lance le script toutes les 10 min: ``*/10 * * * * /usr/local/bin/monitoring.sh``
cron (chrono tab -> "table de planification") est un programme qui permet aux utilisateurs des systèmes Unix d’exécuter automatiquement des scripts, des commandes ou des logiciels à une date et une heure spécifiée à l’avance, ou selon un cycle défini à l’avance.
