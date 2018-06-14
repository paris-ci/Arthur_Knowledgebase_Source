---
title: "Installer Windows 10 Sur Un Disque Externe Depuis Un Mac"
tags: ["windows", "mac os", "bootcamp"]
date: 2018-06-13T00:16:18+02:00
draft: false
---

Il peut être intéressant d'installer windows sur votre mac avec boot camp pour lancer des jeux ou travailler avec des programmes non compatibles avec Wine ou Mono. Mais par manque de place, de puissance, ou simplement lorsque une machine virtuelle ne suffit plus, il reste une solution : installer windows sur un disque dur ou SSD externe à votre mac. En quelques heures seulement, vous aurez une version totalement fonctionnelle de Windows, sur disque externe, avec un démarrage possible depuis un mac.

Ce tutoriel est inspiré de [celui-ci](http://bleeptobleep.blogspot.fr/2013/02/mac-install-windows-7-or-8-on-external.html "Article en anglais avec windows 7") mais il corrige des erreurs et évoque des problèmes que j'ai rencontré.
<!--more-->
{{< warning >}}
Ce tutorial vous demandera de redemarrer votre ordinateur à plusieurs reprises. Il serait utile de le suivre depuis un autre appareil
{{< /warning >}}
Remplissez les paramètres suivant au fur et à mesure que vous suivez le tutoriel.

{{< variables_form >}}

{{<variables_add value="1" name="windows_drive_number" desc="Chiffre assigné à votre disque externe dans la VM de windows">}}
{{<variables_add value="C:\wim\install.wim" name="install_wim" desc="Emplacement du fichier install.wim">}}
{{<variables_add value="B" name="boot_drive_letter" desc="Lettre assignée à la partition de boot, elle ne doit pas être utilisée sur votre système">}}
{{<variables_add value="O" name="primary_drive_letter" desc="Lettre assignée à la partition primaire, elle ne doit pas être utilisée sur votre système">}}
{{<variables_add value="C:\imagex\imagex.exe" name="imagex" desc="Emplacement de l'utilitaire imageex">}}
{{<variables_add value="1" name="installation_index" desc="Index de l'installation de Windows 10 de votre ISO">}}

{{< /variables_form >}}

## Préparation

Commencez par télécharger les outils nécessaires.

* [Une iso de Windows 10](https://www.microsoft.com/fr-fr/software-download/windows10)
* [VMWare fusion](https://download3.vmware.com/software/fusion/file/VMware-Fusion-8.5.8-5824040.dmg) si vous n'avez pas de logiciel de virtualisation (Virtual box ne marche PAS pour ces manipulations, même avec l'extension USB3)

Installez Windows 10 dans une nouvelle VM.

Dans la VM, téléchargez [GetWaikTools](http://www.rmprepusb.com/tutorials/getwaiktools/GetWaikTools.zip?attredirects=0&amp;d=1), et ouvrez le programme

A l'aide de GetWaikTools, récupérez les **ADK Tools for Win 10**.

## Preparation de install.wim et imagex.exe

Déplacez imagex depuis {{< variables_box_highlight_inline >}}ADK_6\amd64\DSIM\imagex.exe{{< /variables_box_highlight_inline >}} vers {{< variables_box_highlight_inline >}}§§imagex§§{{< /variables_box_highlight_inline >}}
Enfin, copiez depuis l'ISO connectée à la VM le fichier {{< variables_box_highlight_inline >}}sources/install.wim{{< /variables_box_highlight_inline >}}

**Remplissez le chemin vers le fichier install.wim dans les paramètres de l'article, ou placez le dans {{< variables_box_highlight_inline >}}§§install_wim§§{{< /variables_box_highlight_inline >}} **

## Partitionnement du disque externe
Branchez votre disque externe, si ce n'est pas déjà fait.

Ouvrez un invite de commandes en mode administrateur (clic droit, exécuter en tant qu'administrateur)

Ouvrez l'utilitaire de partitionnement avec la commande {{< variables_box_highlight_inline >}}diskpart{{< /variables_box_highlight_inline >}}

Tapez la commande {{< variables_box_highlight_inline >}}list disk{{< /variables_box_highlight_inline >}}

Vérifiez que la lettre du disque connecté est bien celle qui est précisée dans les paramètres, c'est à dire {{< variables_box_highlight_inline >}}§§windows_drive_number§§{{< /variables_box_highlight_inline >}}

Sélectionnez votre disque : {{< variables_box_highlight_inline >}}select disk §§windows_drive_number§§{{< /variables_box_highlight_inline >}}

Supprimez tout le contenu du disque avec la commande {{< variables_box_highlight_inline >}}clean{{< /variables_box_highlight_inline >}}

Convertissez le en MBR : {{< variables_box_highlight_inline >}}convert mbr{{< /variables_box_highlight_inline >}}

Creez la partition de boot : {{< variables_box_highlight_inline >}}create partition primary size=350{{< /variables_box_highlight_inline >}}

Formatez cette partition en FAT32 : {{< variables_box_highlight_inline >}}format fs=fat32 quick{{< /variables_box_highlight_inline >}}

Activez la partition : {{< variables_box_highlight_inline >}}active{{< /variables_box_highlight_inline >}}

Assignez lui la lettre {{< variables_show "boot_drive_letter" >}} :{{< variables_box_highlight_inline >}}assign letter=§§boot_drive_letter§§{{< /variables_box_highlight_inline >}}

Creez une nouvelle partition primaire : {{< variables_box_highlight_inline >}}create partition primary{{< /variables_box_highlight_inline >}}

Formattez la en NTFS : {{< variables_box_highlight_inline >}}format fs=ntfs quick{{< /variables_box_highlight_inline >}} 

Assignez lui la lettre {{< variables_show "primary_drive_letter" >}} : {{< variables_box_highlight_inline >}}assign letter=§§primary_drive_letter§§{{< /variables_box_highlight_inline >}}

Enfin, fermez l'utilitaire de partitionnement : {{< variables_box_highlight_inline >}}exit{{< /variables_box_highlight_inline >}}

**Ne fermez pas l'invite de commandes...**

## Déployer l'image de windows

Il est possible et même probable que votre ISO ait plusieurs options d'installation disponibles. Pour les lister, tapez la commande suivante : {{< variables_box_highlight_inline >}}§§imagex§§ /info §§install_wim§§{{< /variables_box_highlight_inline >}}

**Remplissez dans les préférences votre index d'installation correspondant à la version 64 bits que vous souhaitez installer.**

Installez windows sur ce disque avec la commande 

{{< variables_box_highlight >}}
§§imagex§§ /apply §§install_wim§§ §§installation_index§§ §§primary_drive_letter§§:
{{< /variables_box_highlight >}}

Installez le système de démarrage avec la commande suivante : 

{{< variables_box_highlight >}}
§§primary_drive_letter§§:\windows\system32\bcdboot §§primary_drive_letter§§:\windows /f ALL /s §§boot_drive_letter§§:
{{< /variables_box_highlight >}}


> Si vous avez un message d'erreur indiquant que vous ne pouvez pas éxécuter ce programme sur votre PC, c'est que vous etes sur une version 32 bits de windows et que vous essayez d'installer une version 64 bits. Veuillez choisir une version 64 bits de windows 10 lors de l'installation de votre VM ;) Je ne crains qu'il faille recommencer le tutorial du début.

## Installation de Windows

C'est le moment fatidique. Eteignez la VM, activez le son sur votre mac et éteignez le. Ne débranchez pas votre disque externe.

Au redémarrage de votre mac, lorsque vous entendez le son de démarrage, appuyez et maintenez la touche **Alt/Option** appuyée. Sélectionnez le disque orange Windows/EFI Boot et tapez entrée.

Suivez les instructions d'installation normales. Lorsque le programme d'installation de windows redémarre l'ordinateur, suivez les instructions précédentes pour démarrer sur windows.

Il est possible que vous ayez besoin d'une souris et d'un clavier USB (astuce : utilisez le clavier virtuel de Windows) pour passer les étapes...
## Installation des drivers Bootcamp

Ouvrez l'assistant bootcamp une fois sous mac, et cliquez sur action dans la barre de menu, téléchargez les drivers bootcamp et placez les dans une clé USB ou sur votre installation windows (Il est facile, avec [NTFS for Mac Os](https://www.paragon-software.com/ufsdhome/fr/ntfs-mac/), d'acceder en lecture/ecriture à votre disque externe Windows).

Si l'assistant bootcamp ne vous propose pas cette option, vous pouvez essayer de télécharger les drivers depuis [le site d'apple](https://support.apple.com/kb/dl1720?locale=fr_FR) directement.

Redémarrez de nouveau sous Windows.

Ouvrez {{< variables_box_highlight_inline >}}setup.exe{{< /variables_box_highlight_inline >}} dans {{< variables_box_highlight_inline >}}WindowsSupport\WindowsSupport\BootCamp{{< /variables_box_highlight_inline >}} en tant qu'administrateur

Redémarrez autant de fois que nécéssaire pour que l'ensemble de vos périphériques marchent.

## Mise à jour des drivers

Lancez Apple Software Update et mettez à jour l'ensemble des drivers. Redémarrez lorsque demandé.

## Disposition de clavier apple

Si le clavier apple n'est pas bien reconnu par windows (Touche - affichant =, ...), installez [cette disposition de clavier](https://phocean.net/wp-content/uploads/2013/11/fr_mac.zip)

Décompressez le .zip et lancez setup.exe

Dans votre barre des taches, cliquez sur la touche FRA FR, puis sur préférences linguistiques, francais (france), options, francais AZERTY, supprimer.

Fermez la fenetre.

Testez votre clavier et enjoy :)
