Projet du 2ème cercle du cursus 42

# Consignes:
**Mettre en place un serveur en suivant des règles spécifiques.**

Utiliser VirtualBox, avec Debian comme OS. SElinux et Apparmor doivent rester actifs.

Deux partitions chiffrées doivent être créées avec LVM. Un service SSH doit être actif sur le port 4242 (qui doit être le seul port ouvert); l'utilisateur root ne doit pas pouvoir se connecter via SSH.

L'OS doit être configurée avec le pare-feu UFW.

Le hostname = cyglardo42.

Politique de mot de passe fort: 
- mot de passe expire tous les 30 jours: au moins 7 nouveaux char comparé à l'ancien, sauf pour l'utilisateur root.
- min. 2 jours entre chaque changement de mots de passe
- avertissement 7 jours avant la limite pour changer le mot de passe
- min. 10 char dont 1 maj, 1 min, 1 chiffre et pas plus de 3 rep consécutives d'un même char. Ne doit pas être ou contenir le nom d'utilisateur.
Installation et configuration stricte de sudo
Un utilisateur en plus de l'utilisateur root; il appartient à sudo et users42.

Attention, une fois tout configuré, il faudra changer tous les mots de passe.
