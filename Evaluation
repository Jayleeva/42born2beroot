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
