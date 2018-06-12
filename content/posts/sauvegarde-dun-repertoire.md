---
title: "Sauvegarde D'un Repertoire"
date: 2018-06-13T00:08:33+02:00
draft: false
---

Il est important de mettre régulièrement en place des sauvegardes afin de réduire le risque de perte de données. De même que pour les backups de vos bases de données mysql, il est aussi important de sauvegarder, par exemple, les fichiers de votre site, si vous effectuez des modifications régulièrement, ou si vos utilisateurs envoient du contenu.


<!--more-->
{{< variables_form >}}

{{<variables_add value="/var/bck/monsite" name="emplacement_du_backup" desc="Emplacement de l'archive à générer">}}
{{<variables_add value="/var/www/" name="emplacement_a_backup" desc="Dossier à sauvegarder">}}

{{< /variables_form >}}


Vous pouvez par exemple utiliser ce simple 1-liner en bash

{{< variables_box_highlight language="bash" >}}tar -cpzf §§emplacement_du_backup§§ §§emplacement_a_backup§§{{< /variables_box_highlight >}}
