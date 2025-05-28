---
title: "Tux Fan Club: Chasse au Trésor Pirate"
description: "Cap'tain René Bojolais est un petit cachotier."
icon: "savings"
date: "2025-05-07T00:44:31+01:00"
lastmod: "2025-05-07T00:44:31+01:00"
draft: false
toc: true
weight: 600
---

## Treasure Hunt 🪙

### Découverte d'un mystérieux fichier...

Nous avons décidé d'accepter une *quête secondaire* durant le projet `Rootkit`.

En effet, comme tout bon pirate, nous avons téléchargé _légalement_ les fichiers fournis (`given files`) et nous souhaitions les ignorer avec un fichier `.gitignore`.

Le `.gitignore` ressemblait à :

```bash
.gitignore
givenfiles/
```

Sauf que les fichiers n'étaient pas ignorés. _(Vu que Léa ne sait pas écrire)_ C'est là que nous découvrons un mystérieux fichier `.README` :

{{< figure src="/images/ctf/givenfilestree.png" alt="Arborescence révélant le fichier caché" >}}
*Arborescence révélant le fichier caché*

### Tentative de lecture du parchemin

Après avoir accédé à ce fichier, nous décidons de l'ouvrir. Le déchiffrer était une autre paire de manches...

*Contenu du fichier mystérieux*
```text
W3lc0m3 t0 thE 53cR3t p1r4t35 l4ir.

Young pirate, you have find my treasure.

It is yours.

Navigate safely,

--
Cap'tain René Bojolais
```

Le reste du parchemin était du langage codé. Grâce à notre expérience en programmation et en langues, nous comprenons qu'il s'agit d'une image encodée en base64.

### Déchiffrer l'image

Avec la commande `base64 -d .README > image.jpg` nous avons pu obtenir l'image (il fallait évidemment garder que la partie en base64 du `README`).

{{< figure src="/images/ctf/SOT.jpg" alt="Image déchiffrée" class="img-fluid" >}}
*Image déchiffrée*

Voici la magnifique illustration... Mais nous savons que le capitaine René avait plus d'un tour dans son sac. Donc nous décidions d'examiner l'image en détail.

### Chanson cachée

En utilisant le site [https://georgeom.net/StegOnline/upload](https://georgeom.net/StegOnline/upload) pour extraire les valeurs RGB de l'image et le texte caché, on remarque une chaîne de caractères insolite...

{{< figure src="/images/ctf/extract.png" alt="Extraction de données cachées" >}}
*Extraction de données cachées*

> I am captain Jack Sparrow - Remix high bass quality - SAVAGE MIX.wavUT

Cela nous rappelle une chanson de pirates bien connue...

Notre première idée est de chercher le remix sur Youtube afin de voir si le Capitaine René n'a pas laissé un petit flag en commentaire. Apparemment, il n'est pas fan d'OSINT, dommage 😔

Notre deuxième idée est de dézipper l'image `unzip image.jpg` et là, bingo !

```bash
Archive:  image.jpg
warning [image.jpg]:  21054 extra bytes at beginning or within zipfile
  (attempting to process anyway)
  inflating: I am captain Jack Sparrow - Remix high bass quality - SAVAGE MIX.wav
```

### Audacity time

Grâce à notre expérience en CTF, nous avons tout de suite l'idée d'examiner le spectrogramme.

Rien d'anormal sauf sur les dernières secondes du fichier. Et là... nous trouvons le flag !

{{< figure src="/images/ctf/flag.png" alt="Le trésor caché dans le spectrogramme" >}}
*Le trésor caché dans le spectrogramme*

Il ne nous reste qu'à transmettre le mot de passe au capitaine et à nous le trésor...

### Trésor

{{< figure src="/images/ctf/treasure.jpeg" alt="Récompense du Capitaine" >}}
*Grrr des canards*

## Conclusion

Une fois cette chasse au trésor finie, il est temps de commencer le projet principal 👀
