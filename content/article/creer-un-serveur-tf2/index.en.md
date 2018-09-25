---
title: "Create a TF2 server"
categories: ["TF2", "Linux", "Server"]
date: 2018-07-24T00:00:00+02:00
draft: false

slug: "create-a-tf2-server"
---

TF2 is a fun game I'd like people to play on. Unfortunately, it's not as easy to set up a TF2 server (compared to, IE, a minecraft server), but I did the work for you with this tutorial :).

<!--more-->
I assume you are using Ubuntu 18.04 (latest LTS at the time of writing).

As usual, fill the variable box down here to personalize the tutorial for you. The values aren't stored anywhere, so you can be sure anything you put there is private.

{{< variables_form >}}

{{<variables_add value="tf.api-d.com" name="dns" desc="Your TF2 server domain name">}}
{{<variables_add value="tf2server" name="user" desc="User that will be created and will own the tf2 server files">}}

{{< /variables_form >}}

## Prerequisites

If you want to proceed with the server installation, you'll need :

* A VPS or a linux (ubuntu) box to run the bot. You can get them for cheap at ScaleWay (a START1-M or L will do just fine, depending on the guild count) or DigitalOcean, but you **can't get them for free**

I used a VPS - GAME1 from [HostMyServers](https://www.hostmyservers.fr), since they make affordable 5gHz VPSand are located in France (closer to me in the network point of view)

* An internet connection
* A basic understanding of terminal and SQL
* A steam account
* Optional, but recommended : a domain name. I use Gandi for that, but you can use anyone that sells domains names.

That's alright ? Let's get started!


## Server setup 

Login to your server using SSH
{{< variables_box_highlight language="bash" >}}ssh 'root@§§dns§§"'{{< /variables_box_highlight >}}

Update everything on the server to start cleanly.

{{< variables_box_highlight language="bash" >}}apt update && apt dist-upgrade -y{{< /variables_box_highlight >}}

Install a web server (apache2), a SQL database (mysql-server + mysql-client) and PhpMyAdmin to administrate the Database.

{{< variables_box_highlight language="bash" >}}apt install -y apache2 mysql-server mysql-client phpmyadmin{{< /variables_box_highlight >}}

When requested, select apache2 with space then use tab to select the OK button 

Choose yes when asking to configure the database with dbconfig-common

The password will be root, repeat two times.

Prepare the server by installing LGSM prerequisites :

{{< variables_box_highlight language="bash" >}}dpkg --add-architecture i386; apt update; apt install -y mailutils postfix curl wget file bzip2 gzip unzip bsdmainutils python util-linux ca-certificates binutils bc jq tmux lib32gcc1 libstdc++6 libstdc++6:i386 libcurl4-gnutls-dev:i386 libtcmalloc-minimal4:i386{{< /variables_box_highlight >}}

When prompted, you want a local site with a FQDN of {{< variables_box_highlight_inline language="none" >}}{{< /variables_box_highlight_inline >}}


Install some more TF2 server prerequisites:

{{< variables_box_highlight language="bash" >}}apt install -y gdb lib32tinfo5{{< /variables_box_highlight >}}

Cleanup behind ourselves :

{{< variables_box_highlight language="bash" >}}apt autoremove{{< /variables_box_highlight >}}

## Create the TF2 server user

Once done, create the user with
{{< variables_box_highlight_inline language="none" >}}adduser §§user§§{{< /variables_box_highlight_inline >}}

Enter a secure, random password that will not show up when you type. Press enter at every prompt.

Login as the user with 

{{< variables_box_highlight language="none" >}}su - §§user§§{{< /variables_box_highlight >}}




## Install LGSM

Linux Game Server manager provide a simple command to install itself
{{< variables_box_highlight language="none" >}}wget -O linuxgsm.sh https://linuxgsm.sh && chmod +x linuxgsm.sh && bash linuxgsm.sh tf2server{{< /variables_box_highlight >}}

Then, type, as requested, 
{{< variables_box_highlight language="none" >}}./tf2server install{{< /variables_box_highlight >}}


Press enter when asked for the server directory (it should already be {{< variables_box_highlight_inline language="none" >}}/home/{{user}}{{< /variables_box_highlight_inline >}})

The install should proceed, press enter when asked if it completed successfully.

## Get the GSLT token

Go to [this webpage](https://steamcommunity.com/dev/managegameservers), ensured you are logged in, and create a new token (game number will be 440, use a memo like 'My TF2 server')

Copy your token and paste it into the prompt when asked, the press enter.

## Start the server 

Type {{< variables_box_highlight_inline language="bash" >}}./tf2server start{{< /variables_box_highlight_inline >}} to start the server for the first time

Type {{< variables_box_highlight_inline language="bash" >}}./tf2server console{{< /variables_box_highlight_inline >}} to view the server console. 
Wait until the server is started (You'll see a message like "Server is hibernating")

Exit the console with CTRL-B and then type D

## Stop the server 

Type {{< variables_box_highlight_inline language="bash" >}}./tf2server stop{{< /variables_box_highlight_inline >}} to stop the server now that the files have been created.

## Install mods

### Install metamod

Install metamod (required by sourcemod) by typing {{< variables_box_highlight_inline language="bash" >}}./tf2server mi{{< /variables_box_highlight_inline >}}, then metamod

### Install sourcemod

Repeat the process with Sourcemod, by typing {{< variables_box_highlight_inline language="bash" >}}./tf2server mi{{< /variables_box_highlight_inline >}}, then sourcemod

### Enable fastdl

Enable fastDl using the following command {{< variables_box_highlight_inline language="bash" >}}./tf2server fastdl{{< /variables_box_highlight_inline >}}

Type Y, this is gonna take a long time.

You can and should re-run this command each time you update a map or something the clients have to download.

### Update the tf2server config

Open the config file with {{< variables_box_highlight_inline language="bash" >}}nano ./serverfiles/tf/cfg/tf2server.cfg{{< /variables_box_highlight_inline >}} and add at the end

{{< variables_box_highlight language="none" >}}
// FastDL
sv_downloadurl "http://§§dns§§/fastdl"
sv_allowdownload 0
{{< /variables_box_highlight >}}

### Update the apache2 config

Create a new fastdl file with (as root, open a new terminal if needed) {{< variables_box_highlight_inline language="bash" >}}nano /etc/apache2/sites-available/§§dns§§.conf{{< /variables_box_highlight_inline >}}

{{< variables_box_highlight language="apache2" >}}
<VirtualHost *:80>
        ServerName §§dns§§
        ServerAlias §§dns§§
        DocumentRoot /home/§§user§§/public_html
        <Directory /home/§§user§§/public_html>
                Options Indexes FollowSymLinks MultiViews
                AllowOverride All
                Require all granted
        </Directory>
        ErrorLog ${APACHE_LOG_DIR}/error.log
        # Possible values include: debug, info, notice, warn, error, crit, alert, emerg.
        LogLevel warn
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
{{< /variables_box_highlight >}}

And then, type {{< variables_box_highlight_inline language="bash" >}}a2ensite §§dns§§.conf && systemctl reload apache2{{< /variables_box_highlight_inline >}} to enable the config you created. 

## Make the server start on boot, and also update it every so often

Type {{< variables_box_highlight_inline language="bash" >}}crontab -e{{< /variables_box_highlight_inline >}} and add in the file the following line

{{< variables_box_highlight language="crontab" >}}
@reboot /home/§§user§§/tf2server monitor > /dev/null 2>&1
*/5 * * * *  /home/§§user§§/tf2server monitor > /dev/null 2>&1
@hourly /home/§§user§§/tf2server update > /dev/null 2>&1
30 0 * * * /home/§§user§§/tf2server update-lgsm > /dev/null 2>&1 && /home/§§user§§/tf2server force-update > /dev/null 2>&1
{{< /variables_box_highlight >}}

