---
title: "How to install DuckHunt and DuckHunt community on your server"
date: 2018-07-16T12:26:13+02:00
draft: false

categories: ['Linux', 'Tutorials']
tags: []
---

This article is a step-by-step tutorial on how to install DuckHunt, and the support server bot, DuckHunt Community.
If you want to help developing the bot, this tutorial is the official way to setup your developpement environnemnt

Let's start by a recap of what DuckHunt (*DH*), and DuckHunt-Community (*DHC*) is.
<!--more-->

## What is DuckHunt
DuckHunt is a discord game bot, created following MenzAgitat own DuckHunt IRC bot. The primary goal of the game is to kill ducks.
To make the game more fun and entertaining, a big number of commands, shop items, special ducks, have been added since the first version.

Of course, hosting a big game across so many discord servers (*guilds*) require a great support team. The support is provided, on the
[official DuckHunt Support Server](https://discord.gg/2BksEkV). To help with moderating the previous server, a custom bot has been developed : DuckHunt-Community
 
Both bots use the latest version of python on discord.py at the time of writing, respectively 3.6 and the rewrite branch.

The game has been open-sourced on [GitHub](https://github.com/duckhunt-discord/DHV3).

### Where can I find help on the DuckHunt Bot
If you don't want to install DuckHunt, but just want to use it, **you are in the wrong place**. An up-to-date website with the command list, the settings,
usage, FAQs, and the installation tutorial to get the official bot on your server are available on the [Bot Website](https://duckhunt.me)

## Prerequisites

If you want to proceed with the bot installation, you'll need :

* A VPS or a linux (ubuntu) box to run the bot. You can get them for cheap at ScaleWay (a START1-M or L will do just fine, depending on the guild count) or DigitalOcean, but you **can't get them for free**
* An internet connection
* A basic understanding of terminal, and discord bots
* A discord account

Additionally, the configuration used in this tutorial is as follow.

* A START1-M VPS bought at ScaleWay
* The latest ubuntu LTS version : [Bionic Beaver](https://wiki.ubuntu.com/BionicBeaver) (18.04)
* IPv6 Disabled, we don't need it to connect to Discord.

Feel free to change this configuration if needed, but you may need to adapt some parts of the tutorial.

## Roadmap

We'll start by installing dependencies to both bots, then we will fully install DuckHunt, and to finish things up DuckHunt Commmunity


{{< variables_form >}}

{{<variables_add value="127.0.0.1" name="server_IP" desc="IP of the server where you'll install the bots">}}

{{<variables_add value="/home/duckhunt/DH" name="DH_installation_folder" desc="Folder where you'll install DuckHunt">}}

{{<variables_add value="/home/duckhunt/DHC" name="DHC_installation_folder" desc="Folder where you'll install DuckHunt-Community">}}

{{<variables_add value="duckhunt" name="DH_mysql_db_name" desc="Name of the DuckHunt Database">}}

{{<variables_add value="duckhunt" name="DH_mysql_db_user" desc="Name of the DuckHunt Database User">}}

{{<variables_add value="CHANGEME" name="DH_mysql_db_password" desc="Password of the DuckHunt Database User">}}

{{<variables_add value="187636089073172481" name="DH_ID" desc="Your DuckHunt bot ID">}}

{{<variables_add value="XXXXXXXXXXXXXXXX.XXXXXX.XXXXXXXXXXXXXXXX" name="DH_token" desc="Your DuckHunt bot token (not transmitted to any server, but don't take my word for it and check the webpage code by yourself if you are not sure)">}}

{{<variables_add value="306062561021919242" name="DHC_ID" desc="Your DuckHunt-Community bot ID">}}

{{<variables_add value="XXXXXXXXXXXXXXXX.XXXXXX.XXXXXXXXXXXXXXXX" name="DHC_token" desc="Your DuckHunt-Community bot token">}}

{{< /variables_form >}}

## Installation

###  Dependencies

Let's connect to our new server using 
{{< variables_box_highlight language="sh" >}}
ssh root@§§server_IP§§
{{< /variables_box_highlight >}}

Now, let's get the system up-to-date and install dependencies:

{{< variables_box_highlight language="sh" >}}
apt update
apt upgrade -y
apt install -y apt-utils git htop screen apache2 php php-mysql python3-pip python3-dev mysql-client mysql-server libmysqlclient-dev
{{< /variables_box_highlight >}}

Create the DuckHunt User

{{< variables_box_highlight language="sh" >}}
adduser duckhunt
{{< /variables_box_highlight >}}

And provide a password if asked.

Download the code of both bots in the specified directories.

{{< variables_box_highlight language="sh" >}}
su - duckhunt
git clone https://github.com/DuckHunt-discord/DHV3.git §§DH_installation_folder§§
git clone https://github.com/DuckHunt-discord/DuckHunt-Community-Rewrite.git §§DHC_installation_folder§§
exit
{{< /variables_box_highlight >}}

Let's install python requirements for DuckHunt

{{< variables_box_highlight language="sh" >}}
cd §§DH_installation_folder§§
pip3 install -r requirements.txt
{{< /variables_box_highlight >}}

###  DuckHunt

#### Database
Time have come to create the database for DuckHunt.

Login as the root user on the DataBase:

{{< variables_box_highlight language="sh" >}}
mysql -u root -proot
{{< /variables_box_highlight >}}

In the MySQL prompt, to create the user and the database, write 

{{< variables_box_highlight language="sql" >}}
create database §§DH_mysql_db_name§§;
grant all privileges on §§DH_mysql_db_name§§.* to '§§DH_mysql_db_user§§'@'localhost' identified by "§§DH_mysql_db_password§§";
flush privileges;
exit;
{{< /variables_box_highlight >}}

Then, load the schema into the database. 

{{< variables_box_highlight language="sh" >}}
mysql --database=§§DH_mysql_db_name§§ -u §§DH_mysql_db_user§§ -p§§DH_mysql_db_password§§ < §§DH_installation_folder§§/schema.sql
{{< /variables_box_highlight >}}


#### Configuration 

Now that the database is correctly setup, give the bot the credentials to connect to discord.

For this, create a `credentials.json` file using

{{< variables_box_highlight language="sh" >}}
nano §§DH_installation_folder§§/credentials.json
{{< /variables_box_highlight >}}

In this file, add the following :

{{< variables_box_highlight language="json" >}}
{
  "token": "§§DH_token§§",
  "bots_discord_pw_key" : "",
  "discordbots_org_key" : ""
}
{{< /variables_box_highlight >}}

Then, save and quit (CTRL-X + Y).

Open the configuration file with 

{{< variables_box_highlight language="sh" >}}
nano §§DH_installation_folder§§/cogs/helpers/config.py
{{< /variables_box_highlight >}}

At least, locate the lines concerning the database settings and update them to look like this:

{{< variables_box_highlight language="python" >}}
# > Database settings < #
# User used to connect to the Mysql DB
bot.database_user = "§§DH_mysql_db_user§§"

# Password for the user used to connect to the Mysql DB
bot.database_password = "§§DH_mysql_db_password§§"

# Name of the table used in the Mysql DB
bot.database_name = "§§DH_mysql_db_name§§"
{{< /variables_box_highlight >}}

Of course, you can update other settings there.

Then, save and exit (CTRL-X + Y).

#### Starting 

DuckHunt should now be working \o/. To test and start it, type

{{< variables_box_highlight language="sh" >}}
cd §§DH_installation_folder§§ && python3 ./bot.py
{{< /variables_box_highlight >}}

#### WebSite
Let's start by configuring the DB for it :

{{< variables_box_highlight language="sh" >}}
nano §§DH_installation_folder§§/web/duckstats.php
{{< /variables_box_highlight >}}

Locate the lines like this and replace them:

{{< variables_box_highlight language="php" >}}
$dbname   = "§§DH_mysql_db_name§§";
$username   = "§§DH_mysql_db_user§§";
$password   = "§§DH_mysql_db_password§§";
{{< /variables_box_highlight >}}

You can also change the server name in `web/live-ducks.php` if needed


A simple symlink should do the trick to serve it:
{{< variables_box_highlight language="sh" >}}
rm -Rv /var/www/html
ln -s §§DH_installation_folder§§/web /var/www/html 
{{< /variables_box_highlight >}}

Finally, to save the data from the stats API periodically, open the crontab
{{< variables_box_highlight language="sh" >}}
su - duckhunt
crontab -e
{{< /variables_box_highlight >}}

Add the following :

{{< variables_box_highlight language="cron" >}}
0 * * * * cd §§DH_installation_folder§§/web ; python3 ./save_historical_to_csv.py
{{< /variables_box_highlight >}}

Exit nano as usual, and type `exit` to return to the root prompt


### DuckHunt Community

DuckHunt Community should be really easier to install, since we don't require a database for it.

#### Create necessary directories

To let DuckHunt-Community store data about bans and stuff, we'll need the following directory structure.
{{< variables_box_highlight language="sh" >}}
mkdir §§DHC_installation_folder§§/mods
mkdir §§DHC_installation_folder§§/mods/cases
mkdir §§DHC_installation_folder§§/mods/users
echo '1' > §§DHC_installation_folder§§/mods/current_case.txt
mkdir §§DHC_installation_folder§§/users
{{< /variables_box_highlight >}}

#### Configuration 
Again, give the bot the credentials to connect to discord.

For this, create a `credentials.json` file using

{{< variables_box_highlight language="sh" >}}
nano §§DHC_installation_folder§§/credentials.json
{{< /variables_box_highlight >}}

In this file, add the following :

{{< variables_box_highlight language="json" >}}
{
  "token": "§§DHC_token§§"
}
{{< /variables_box_highlight >}}

### Last Steps

#### Auto-restart

You'll probably want the bot to restart every time it fails, or when you use the `dhexit` command. Feel free to use [this tutorial]({{< relref "article/lancer-un-processus-au-demarrage/index.md" >}}) for this.

#### Backup Script

