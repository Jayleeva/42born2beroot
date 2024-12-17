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
``lsblk`` : affiches les partitions

