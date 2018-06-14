---
title: "Lancer Un Processus Au Démarrage"
tags: ["linux"]
date: 2018-06-13T10:12:17+02:00
draft: false
---

Il est parfois utile de lancer un processus lorsqu'un serveur ou qu'un ordinateur linux démarre.
Sur Mac OS, c'est plutot simple, il suffit de cliquer sur le processus dans le dock. 
Malheureusement, sous linux, avec une console, c'est tout de suite plus compliqué...
<!--more-->

{{< variables_form >}}

{{<variables_add value="mon_programme" name="name" desc="Nom du programme, doit être unique sur votre système, et permet de reconnaitre le processus lancé">}}
{{<variables_add value="/home/$USER/start.sh" name="cmd" desc="Commande de lancement du programme">}}
{{<variables_add value="$USER" name="user" desc="(facultatif) Utilisateur devant lancer la commande">}}

{{< /variables_form >}}

## Méthode 1 : Utiliser votre crontab

### Avantages de la méthode

* Simple à configurer
* Lance un programme en votre nom

### Points à considèrer

* Impossible de voir/intéragir avec la console
* Si votre programme affiche des choses, elles vous seront envoyées par mail local

### Comment l'utiliser

Ouvrez votre crontab avec {{< variables_box_highlight_inline language="bash" >}}crontab -e{{< /variables_box_highlight_inline >}}
puis ajoutez la ligne suivante : 

{{< variables_box_highlight language="cron" >}}@reboot §§cmd§§{{< /variables_box_highlight >}}

## Méthode 2 : Utiliser la crontab du systeme

### Avantages de la méthode

* Simple à configurer
* Lance un programme en tant qu'un autre utilisateur

### Points à considèrer

* Impossible de voir/intéragir avec la console
* Nécéssite d'être root pour écrire dans la crontab systeme.

### Comment l'utiliser

Ouvrez la crontab avec votre éditeur favori: {{< variables_box_highlight_inline language="bash" >}}sudo nano /etc/crontab{{< /variables_box_highlight_inline >}}

puis ajoutez la ligne suivante : 

{{< variables_box_highlight language="cron" >}}@reboot §§user§§ §§cmd§§{{< /variables_box_highlight >}}

## Méthode 3 : Utiliser systemd

### Avantages de la méthode

* Extrèmement configurable
* Lance un programme en tant qu'un autre utilisateur
* Permet de simplifier les scripts de lancement en prennant en compte le re-lancement,
 la vérification de certaines conditions, le changement de répertoire, et bien plus

### Points à considèrer

* Impossible de voir/intéragir avec la console
* Nécéssite d'être root pour écrire dans la crontab systeme.
* Plus complexe à configurer 

### Comment l'utiliser

Creez et ouvrez le fichier service dans votre éditeur favori : {{< variables_box_highlight_inline language="bash" >}}sudo nano /etc/systemd/system/§§name§§.service{{< /variables_box_highlight_inline >}}

Ajoutez les lignes suivantes :

{{< variables_box_highlight language="systemd" >}}
# Ceci est un service systemd basique crée à l'aide de 
# kb.api-d.com/posts/lancer-un-processus-au-demarrage

# Pour en savoir plus sur les options disponibles
# la documentation est disponible ici :
# https://www.freedesktop.org/software/systemd/man/systemd.unit.html#%5BUnit%5D%20Section%20Options

[Unit]
After=network.target
Description=Lancement au démarrage de §§name§§
# Vérifier qu'un répertoire existe au démarrage
#AssertPathExists=/what/ever
[Service]

# Un service simple
Type=simple

# Commande de démarrage
ExecStart=§§cmd§§

# Commande d'arret, si nécéssaire
#ExecStop=§§cmd§§ stop

# Répertoire de travail du service, si nécéssaire
WorkingDirectory=/home/§§user§§

# Utilisateur et groupe
User=§§user§§
Group=§§user§§

# Redémarrage en cas de plantage
Restart=on-failure # ou always, on-abort, etc

# Temps avant lequel le service sera tué lors d'une commande stop si celle-ci ne réponds pas
TimeoutStopSec=300

# Valeur de nice du service
#Nice=5

[Install]
WantedBy=multi-user.target

{{< /variables_box_highlight >}}

N'oubliez pas de rendre votre script exécutable:

{{< variables_box_highlight language="bash" >}}

chmod 744 §§cmd§§

{{< /variables_box_highlight >}}

Enfin, préparez et lancez votre service:

{{< variables_box_highlight language="bash" >}}
chmod 664 /etc/systemd/system/§§name§§.service
systemctl daemon-reload
systemctl enable §§name§§.service

{{< /variables_box_highlight >}}

Pour lancer votre service immédiatement, tapez : 

{{< variables_box_highlight language="bash" >}}

systemctl start §§name§§.service

{{< /variables_box_highlight >}}

## Informations supplémentaires

{{< info >}}
Il est plutôt simple de pouvoir voir et intéragir avec 
la console du service si cela est utile dans votre cas,
en lancant le programme dans un screen

{{< /info >}}

Pour cela, utilisez la commande suivante:

{{< variables_box_highlight language="bash" >}}

# Le exec bash permet de garder le screen vivant.
/usr/bin/screen -dmS §§name§§ bash -c '§§cmd§§; exec bash'

{{< /variables_box_highlight >}}

Et, si l'on veut une boucle, par exemple dans le cas de la crontab :

{{< variables_box_highlight language="bash" >}}

/usr/bin/screen -dmS §§name§§ bash -c 'while true; do §§cmd§§; sleep 5; done; exec bash'

{{< /variables_box_highlight >}}

