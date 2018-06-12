---
title: "Afficher Un Traceroute Sur Une Carte"
date: 2018-06-13T00:12:17+02:00
draft: false
---

Pour afficher un traceroute de façon graphique sur une carte, il est possible d’utiliser l’excellent traceroute mapper en y collant la sortie de la commande traceroute, mais il est aussi possible d’utiliser la commande suivante pour ouvrir la page web directement depuis le terminal.

<!--more-->

{{< variables_form >}}

{{<variables_add value="google.fr" name="ip" desc="Nom de domaine ou IP vers lequel effectuer le traceroute">}}
{{<variables_add value="open" name="open_cmd" desc="Commande pour ouvrir une page web sur votre machine">}}

{{< /variables_form >}}


{{< variables_box_highlight language="bash" >}}§§open_cmd§§ "http://stefansundin.github.io/traceroute-mapper/?trace=$(traceroute -q1 §§ip§§)"{{< /variables_box_highlight >}}
