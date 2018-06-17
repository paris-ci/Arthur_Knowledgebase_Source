---
title: "Display a traceroute on a map"
categories: ["Terminal", "Linux", "Mac OS"]
date: 2018-06-13T00:12:17+02:00
draft: false

slug: "display-traceroute-on-a-map"
---

To display a traceroute graphically on a map, it is possible to use the excellent traceroute mapper by pasting the output of the traceroute command to it, 
but it is also possible to use the following command to open the web page directly from the terminal.

<!--more-->

{{< variables_form >}}

{{<variables_add value="google.fr" name="ip" desc="Domain name or IP address where you want the traceroute to go">}}
{{<variables_add value="open" name="open_cmd" desc="Command to open a webpage on your machine">}}

{{< /variables_form >}}


{{< variables_box_highlight language="bash" >}}§§open_cmd§§ "http://stefansundin.github.io/traceroute-mapper/?trace=$(traceroute -q1 §§ip§§)"{{< /variables_box_highlight >}}
