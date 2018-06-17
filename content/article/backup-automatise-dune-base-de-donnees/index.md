---
title: "Backup automatisé d’une base de données"
categories: ["Terminal", "Mac OS", "Linux"]
date: 2018-06-12T18:20:53+02:00
draft: false
---

Il est important de mettre régulièrement en place des sauvegardes afin de réduire le risque de perte de données.
Cet article va vous permettre de générer une commande BASH pour réaliser un backup entier de votre serveur SQL.

<!--more-->
{{< variables_form >}}

{{<variables_add value="database" name="filename" desc="Nom du fichier">}}
{{<variables_add value="root" name="username" desc="Nom d'utilisateur de la base de données">}}
{{<variables_add value="toor" name="password" desc="Mot de passe de la base de données">}}

{{< /variables_form >}}

Pour sauvegarder une base de données facilement, utilisez ce 1-liner en bash

{{< variables_box_highlight language="bash" >}}mysqldump --all-databases -u §§username§§ -p§§password§§ | pv -cN 'Sortie DUMP' | gzip | pv -cN 'Sortie compressée' > §§filename§§.sql.gz && reset{{< /variables_box_highlight >}}


