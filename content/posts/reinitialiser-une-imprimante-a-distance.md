---
title: "Réinitialiser Une Imprimante À Distance"
tags: ["terminal", "mac os", "linux"]

date: 2018-06-13T00:19:17+02:00
draft: false
---

Si l’une de vos imprimantes expose son port 161, elle est surement contrôlable à l’aide du protocole SNMP. Il est possible de la ré-initialiser à distance à l’aide de l’utilitaire snmpset.

<!--more-->
Le fonctionnement en détail, et d’autres vulnérabilités sur les imprimantes sont décrits sur [cette page](https://web-in-security.blogspot.fr/2017/01/printer-security.html)
Il est possible de trouver de telles imprimantes sur [Shodan](https://www.shodan.io/search?query=port%3A161+hp)
{{< variables_form >}}

{{<variables_add value="4" name="action" desc="Action à réaliser sur l'imprimante (voir plus bas)">}}
{{<variables_add value="127.0.0.1" name="ip" desc="IP de votre imprimante">}}

{{< /variables_form >}}

Les actions réalisables sont décrites sur [cette page](http://hacking-printers.net/wiki/index.php/Factory_defaults#SNMP). Il en existe 3 :

* 4 redémarre l’imprimente
* 5 ré-initialise la NVRAM
* 6 effectue une remise des réglages d’usine

{{< variables_box_highlight language="bash" >}} snmpset -v 1 -c public §§ip§§ '.1.3.6.1.2.1.43.5.1.1.3.1' i §§action§§ {{< /variables_box_highlight >}}
