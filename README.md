# TP02_R-seauLinux-

HAMMAIDI KARIM


1.1)  CONNEXION SSH

Comment j'ai fait pour me connecter en ssh:

SUR LA MACHINE VIRTUELLE:
 -on recherche les paquets a mettre a jours avec::apt update
 -si il y en a mettre à jours, alors:: apt upgrade
 -ensuite on recherche les paquets pour installer le ssh avec::apt search ssh
 -si rien n'est installé alors on peut l'installer avec::openssh-server
 -on demarre le serveur ssh avec ::service ssh start
 -avec la commade ::systemctl status ssh , nous pouvons verfifier que le serveur est entrain de fonctionner avec le mot 'Active(running)' inscrit en Jaune.

 Pour mon cas tout cela fonctionner ensuite, sur mon terminal PowerShell de ma machine hôte
    -Pour se conneceter en ssh de la machine hôte sur la machine virtuelle, nous tapons:: ssh root@localhost -p2222  , le '-p2222' indique le port ou se trouver le ssh qui est 22 dans ce cas la 

    UNE ERREUR APPARAIT:
    
          Erreur rencontrée : Le client SSH a détecté un changement dans la clé hôte pour localhost:2222, ce qui a provoqué un échec de la vérification de la clé hôte.
          Solution:
              -Suppression de l'ancienne clé hôte :j'ai utilisé la commande suivante pour supprimer l'entrée de la clé hôte associée à localhost:2222 dans le fichier known_hosts ::ssh-keygen -R [localhost]:2222

              Acceptation de la nouvelle clé : SSH m'a demandé si je voulais accepter la nouvelle clé hôte. j'ai répondu par::yes, ce qui a permis d'ajouter la nouvelle clé hôte dans le fichier 
              known_hosts et de réussir la connexion.

  1.2)   Authentification par clef / Generation de clefs

  -D'abord, nous devons générer un couple de clés SSH
Sur la machine hôte, aveec la commande suivante ::ssh-keygen -t rsa -b 4096 , pour générer une paire de clés SSH (privée et publique) :

  -Ensuite, on copie la clé publique sur le serveur pour permettre l'authentification sans mot de passe.
Sur la machine hôte, on execute cette commande::ssh-copy-id user@server_ip (on remplace user par le nom d'utilisateur qui est ici 'root' et adresse_ip par l'adresse IP du serveur)

-Enfin, on se connecter avec la clé privée
Après avoir copié la clé publique sur le serveur, on se connecte sans mot de passe .:ssh root@adresse_ip

1.3)

-Création du dossier .ssh
    -On commence par vérifier et créer le dossier .ssh dans le répertoire root avec les bonnes permissions :  mkdir -p /root/.ssh
            :  chmod 700 /root/.ssh


-Création et configuration du fichier authorized_keys
Ensuite, on créer le fichier 'authorized_keys' pour stocker la clé publique, et vous avez défini les permissions pour que seul root puisse lire et écrire dans ce fichier :

touch /root/.ssh/authorized_keys
chmod 600 /root/.ssh/authorized_keys


-Ajout de la clé publique
on ajoute la clé publique dans le fichier authorized_keys pour permettre l'authentification par clé:: echo "votre_clé_publique" >> /root/.ssh/authorized_keys

Cette commande a ajouté la clé publique au fichier authorized_keys.

- Enfin, on teste la connexion SSH pour vous assurer que l'authentification par clé fonctionne sans mot de passe ::ssh root@10.0.2.15
Le test a été réussi, confirmant que la configuration.


1.4)


-Réussir à se connecter au serveur en utilisant la commande avec la clé privée et l'adresse IP du serveur.

Voici la commande utilisée ::ssh -i ~/.ssh/id_rsa root@10.0.2.15

Cela montre que la configuration SSH est bien en place et qu'on peut se connecter sans problème au serveur en utilisant l'authentification par clé.

1.5)

Une attaque brute-force SSH consiste à essayer de deviner le mot de passe d’un serveur en testant toutes les combinaisons possibles, jusqu'à ce que la bonne soit trouvée.
Ce processus est souvent automatisé à l'aide de scripts ou d'outils capables de testerà l'infinie des mots de passe.

  par exemple: une entreprise utilise un serveur pour héberger ses applications web. Les administrateurs de l'entreprise se connectent à ce serveur via SSH pour effectuer des tâches comme la 
   maintenance du serveur, les mises à jour, et la gestion des données. Un administrateur à configuré le SSH pour permettre l'accès root (administrateur) avec un mot de passe simple, comme "root".
  L'attaquant utilise un outil de brute-force pour tester les mots de passe sur le serveur de l'entrerpise. Il cible le port SSH par défaut (port 22) et commence à tester des milliers de combinaisons.


-Trouvez et expliquez d’autres techniques permettant `a l’administrateur du serveur de ce prot´eger de ce type
d’attaques::

1. Limiter les tentatives de connexion avec fail2ban
Fail2ban surveille les tentatives de connexion échouées et bloque temporairement l'adresse IP après un certain nombre d'échecs. Cela permet d'arrêter les tentatives de brute-force après quelques essais.
Avantages :
Bloque efficacement les attaques brute-force.

Inconvénients :
Si un utilisateur légitime fait trop d'erreurs de connexion, il peut être bloqué temporairement.

ca ne peut pas être suffisant contre des attaques distribuées provenant de plusieurs adresses IP.
Conclusion : C'est une excellente mesure pour empêcher les attaques en cours, mais il peut être nécessaire de l'utiliser avec d'autres techniques pour une sécurité optimale.


2. Utiliser un pare-feu pour restreindre l'accès SSH
Principe : Un pare-feu comme ufw (Uncomplicated Firewall) peut être utilisé pour limiter l'accès au serveur SSH à des adresses IP spécifiques (par exemple, celles des employés ou des administrateurs).
Avantages :
Réduit fortement les risques d'attaques si vous limitez l'accès uniquement aux adresses IP autorisées.
Excellente mesure pour les entreprises dont les employés ou administrateurs ont des adresses IP fixes.

Inconvénients :
Si l'adresse IP d'un utilisateur légitime change ou qu'il doit se connecter depuis un autre emplacement, il ne pourra pas se connecter à moins que son IP soit ajoutée manuellement.
Conclusion : Très efficace dans un environnement contrôlé (comme une entreprise), mais peu pratique si les utilisateurs ont des IP dynamiques ou doivent accéder au serveur depuis plusieurs endroits.



2 Processus

2.1)


-À quoi correspond l'information TIME ?
TIME représente le temps processeur cumulé utilisé par le processus depuis son démarrage.

-Quel est le processus avant le plus utilisé sur votre machine ?
Pour trouver ce processus, on regarde la colonne %CPU et identifiez le processus avec le pourcentage le plus élevé.

-Quel a été le premier processus lancé après le démarrage du système ?
la commande::ps -eo pid,comm,etime --sort=etime pour afficher les processus triés par leur temps de fonctionnement. Le premier de la liste est probablement le premier processus lancé après le démarrage.

-> ici c'est 'systemd'

-À quelle heure votre machine a-t-elle démarré ?
la commande::who -b pour obtenir l'heure exacte du dernier démarrage.
-> dans ce cas là '2024-10-15 16:51'

-Pouvez-vous établir le nombre approximatif de processus créés depuis le démarrage ?
La commande ps -e --no-headers | wc -l permet d'afficher le nombre total de processus actifs sur la machine.

-> ici il y en a 107


2. Relation entre processus et processus parent (PPID)


Trouver une option pour afficher le PPID d'un processus ::ps -eo pid,ppid,comm     pour afficher à la fois le PID, le PPID, et la commande associée au processus.
Liste des processus ancêtres ::pstree -p <PID>, qui montre un arbre des processus.


3. Utilisation de la commande top


Questions :

-Processus le plus gourmand : on utilise ma commande top et on observe les colonnes %CPU et %MEM pour identifier le processus qui utilise beacoup ressources, en locurrence, ici: CPU qui est a 0,7%

-Les touches interactives dans top ::

M : Trie les processus par utilisation de la mémoire.
P : Trie les processus par utilisation du processeur.
k : Permet de tuer un processus.
q : Quitte l'interface.

-Avantages/inconvénients de top par rapport à ps :
Avantages : top est interactif, il affiche les processus en temps réel et actualisées.
Inconvénients : L'affichage en temps



EXERCICE 3: les tubes


1. Quelle est la différence entre tee et cat ?

-La commande cat lit un fichier ou une entrée et affiche son contenu dans le terminal.
-La commande tee lit l'entrée  mais enregistre également la sortie dans un fichier tout en l'affichant à l'écran.




ls | cat : exécute ls qui liste les fichiers du répertoire actuel et envoie la sortie à cat, qui l'affiche dans le terminal.

ls -l | cat > liste : Le contenu de ls -l sera enregistré dans le fichier liste, mais rien ne sera affiché dans le terminal.

ls -l | tee liste :Résultat : affcighe la liste détaillée des fichiers dans le terminal et elle sera aussi enregistrée dans le fichier liste.

ls -l | tee liste | wc -l : affche la liste détaillée des fichiers dans le terminal et elle sera5 enregistrée dans le fichier liste.


5)    Journal systeme rsyslog

1. Le service rsyslog est-il lancé ? Quel est le PID ?
On utilise la commande:: ps aux | grep rsyslog
Ca indique si rsyslog est actif et affiche le PID qui est ::l'identifiant du processus.


2. Quel est le fichier de configuration principal de rsyslog ?
Le fichier principal est /etc/rsyslog.conf.
Les messages issus des services standards sont enregistrés dans /var/log/syslog.


3. À quoi sert le service cron ?
cron est un service qui exécute des tâches planifiées automatiquement à des intervalles de temps qu'ondéfinis.

4. Que fait la commande tail -f ?
tail -f /var/log/messages permet de visualiser en directLive les nouveaux messages ajoutés dans le fichier messages.


5. À quoi sert le fichier /etc/logrotate.conf ?
Ce fichier configure la rotation des journaux. Il spécifie quand les fichiers de log doivent être compressés, archivés ou supprimés afin de ne pas occuper trop d'espace disque.

6. Que montre la commande dmesg ?
dmesg affiche les informations sur le matériel comme le modèle du processeur, les cartes réseau, etc..






