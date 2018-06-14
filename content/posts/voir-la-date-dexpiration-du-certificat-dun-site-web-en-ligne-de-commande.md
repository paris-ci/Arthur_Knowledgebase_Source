---
title: "Voir la date d’expiration du certificat d’un site web en ligne de commande"
tags: ["terminal", "mac os", "linux"]

date: 2018-06-13T00:19:17+02:00
draft: false
---

Lorsque vous recevez un email de la part de LetsEncrypt vous informant que l’un de vos certificat à expiré,
il peut etre intéressant de vérifier si vous utilisez encore celui-ci, ou si il s’agit d’un certificat différent car, comme précisé dans le mail que vous avez reçu :
<!--more-->

> In particular, note that this reminder email is still sent if you’ve obtained a slightly different certificate by adding or removing names.
> If you’ve replaced this certificate with a newer one that covers more or fewer names than the list above, you may be able to ignore this message. 


{{< variables_form >}}

{{<variables_add value="api-d.com" name="website" desc="Site web HTTPS à vérifier">}}

{{< /variables_form >}}


{{< variables_box_highlight language="bash" >}} echo | openssl s_client -connect §§website§§:443 -servername §§website§§ 2>/dev/null | openssl x509 -noout -dates {{< /variables_box_highlight >}}
