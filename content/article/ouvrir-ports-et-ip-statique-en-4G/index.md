---
title: "Ouvrir ports et obtenir une IP statique en 4G"
categories: ["Routeur 4G", "Réseau", "Linux"]

date: 2019-10-23T00:00:00+02:00
draft: false

slug: "ouvrir-ports-et-ip-statique-en-4G"
---

Ce tutoriel devrait vous permettre soit

- D'ouvrir vos ports en 4G et d'obtenir une IP statique par la meme occasion
- Et possiblement de fusionner des connexions afin d'en sommer la vitesse

Pour cela, nous allons utiliser l'excellent OpenMPTCProuter.

<!--more-->

Il est indispensable pour ce tutoriel de disposer d'un VPS sous Ubuntu 18.04. Je vous conseille un VPS-DEV-S (a 2€99 chez ScaleWay), mais n'importe lequel devrait faire l'affaire.

Il vous faudra aussi disposer de 

- Un Raspberry Pi (2B ou plus récent - Testé avec un 4)
- Une carte micro SD (1Go ou plus), et son lecteur pour le brancher à votre ordinateur.
- [Balena Etcher](https://www.balena.io/etcher/) pour graver les images sur la carte SD
- Une Box internet et/ou un routeur 4G

{{< variables_form >}}

{{<variables_add value="1.1.1.1" name="vps_ip" desc="IP de votre VPS">}}
{{<variables_add value="192.168.1.1" name="box_ip" desc="IP de votre BOX">}}

{{< /variables_form >}}

Commencer par commender votre VPS. Une fois livré, utilisez votre client SSH préféré pour vous connecter dans le serveur : 


{{< variables_box_highlight language="bash" >}}ssh §§vps_ip§§ "wget http://www.openmptcprouter.com/server/ubuntu18.04-x86_64.sh; sudo sh ubuntu18.04-x86_64.sh"{{< /variables_box_highlight >}}

Ca devrait tourner pendant une petite dizaine de minutes. 
Pendant ce temps, téléchargez [ici](https://www.openmptcprouter.com/download) l'image correspondant à votre PI. Choissisez une image factory en ext4 32 bits.

Utilisez Etcher pour graver l'image sur la carte micro SD. 

**Attention, il est possible que les étapes suivantes coupent votre connexion internet.** 

Pensez à télécharger les élèments dont vous auriez besoin, tels que le manuel de votre routeur. Vérifiez aussi que la commande SSH c'est bien terminée et sauvegardez la clé affichée.

À l'aide de votre ordinateur, connectez vous à votre box/routeur 4G. Prenez note de son adresse IP locale, et vérifiez qu'elle n'est pas en 192.168.100.X. Si c'est le cas, changez le 100 en 1.
 
Désactivez le serveur DHCP de votre box, puis redémarrez.

Connectez votre RPI à la box, branchez la carte micro SD et l'alimentation, démarrez le.

Branchez votre ordinateur ou connectez le de nouveau au WiFi. Normalement, il devrait récuperer une IP locale en 192.168.100.XXX.

Connectez-vous sur le [panneau d'administration de OpenMPTCProuter](http://192.168.100.1/).

Dans l'onglet Systeme, ouvrez l'assitant de configuration, et placez-y la clé de configuration qui à été affichée à la fin de l'execution de la commande SSH.
Placez-y aussi l'IP de votre VPS, {{< variables_box >}}§§vps_ip§§{{< /variables_box >}}, et configurez l'IP de votre box/routeur 4G {{< variables_box >}}§§box_ip§§{{< /variables_box >}}

C'est fini! Redemarrez vos appareils et ils devraient automatiquement utiliser la nouvelle connexion et etre assignés une IP locale en 192.168.100.XXX.

Pour l'ouverture de ports, rendez-vous dans les parametres réseau de OpenMPTCProuter!