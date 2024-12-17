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
- Debian = une des OS pour linux
- SElinux = Security Enhanced Linux
- Apparmor =
- partition chiffrée = 
- LVM (Logical Volumes Manager) = sert à gérer les volumes logiques.
- service SSH =
- pare-feu UFW (Uncomplicated FireWall) = 
- hostname =
- sudo = sorte de compte administrateur? en gros pour forcer une commande on utilise sudo
- mode TTY =
- utilisateur root =
- kernel =
- adresse IPv4 =
- adresse MAC =

# Commandes importantes
``lsblk`` : affiche les partitions

``su -`` : permet de se log en tant que superutilisateur (root)

``apt-get update -y`` et ``apt-get upgrade -y`` : permettent de vérifier et mettre à jour (?)

``apt install sudo`` : installe sudo

``usermod -aG sudo your_username`` : ajoute l'utilisateur dans le groupe sudo.

``getent group sudo`` : affiche les membres du groupe sudo.

``sudo visudo`` : ouvre le fichier sudoers (privilèges des membres du groupe?) lire man pour sudoers file. Enregistrer avec ctrl + o et quitter avec ctrl + x.

``apt-get install git -y`` : installe git

``git --version`` : affiche la version git

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

``sudo apt-get install libpam-pwquality`` : installe la librairie pour instaurer une politique de mots de passe?

``sudo vim /etc/pam.d/common-password`` : ouvre le fichier common-password, où on peut changer la politique de mots de passe. A la ligne pam_pwquality.so, après ``retry=3``, ajouter ``minlen=10 ucredit=-1 lcredit=-1 dcredit=-1 maxrepeat=3 reject_username difok=7 enforce_for_root`` (min. 10 char, dont au moins 1 uppercase 1 lowercase et 1 digit, 3 mêmes char à suivre max, au moins 7 char différents de l'ancien mot de passe quand changement, sauf pour root?).

