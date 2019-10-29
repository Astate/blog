---
title: "Comment remettre le stock rom avec Heimdall"
date: 2019-04-15T18:44:34-04:00
tags: [linux,android,heimdall]
draft: false
---

### Télécharger le stock firmware de Samsung
Vous trouverez le firmware à partir du site [sammobile.com](https://www.sammobile.com/firmwares/). Le téléchargement prends quelques heures, alors commancez tout de suite pendant le temps que vous préparer Heimdall.

### Installer heimdall
Il est possible de télécharger la version Os X, Windows et Linux directement sur leur site internet [https://glassechidna.com.au/heimdall/](https://glassechidna.com.au/heimdall/). Si vous avez comme moi, une distribution linux utilsant AUR (Archlinux/Manjaro) vous n'avez tout simplement qu'à l'installer yay.

{{< highlight terminal >}}
        $ yay heimdall
{{< /highlight >}}


### Préparation du flash
Vous devez démarrer le cellulaire en mode Download, enfoncez (volume - power + home). Ensuite vous branchez un bon cable USB (celui d'origine).

#### Vérifez que le cellulaire est reconnu
{{< highlight terminal >}}
        $ heimdall
{{< /highlight >}}
#### Effectuer une copie de la structure de partitionnement
{{< highlight terminal >}}
        $ heimdall print-pit > print-pit.txt
{{< /highlight >}}

Gardez ce ficher "print-pit.txt" qui permetera de faire correspondres les fichiers du firmeware avec les partitions.

Le renseignement du print-pit qui seront utile sont "Partition Name" et "File Name"
exemple:
{{< highlight terminal >}}
        Partition Name: RADIO
        Filename: modem.bin

        Partition Name: RECOVERY
        Filename: recovery.bin
        ...
{{< /highlight >}}


### Extraction du contenu du ROM
Le téléchargement devrait vous générer un fichier comme celui-ci: "G920W8VLU6DRF1_G920W8OYA6DRF1.zip". Vous devez en extraire le contenu. Par la suite vous devriez avoir un fichier de ce type "G920W8VLU6DRF1_G920W8OYA6DRF1_G920W8VLU6DRF1_HOME.md5" que vous devez renommer en changeant le "md5" par "tar". Pour finir, vous n'avez qu'a extraire un dernière fois pour y vois le contenu.

{{< highlight terminal >}}
├── boot.img
├── cache.img
├── cm.bin
├── modem.bin
├── recovery.img
├── sboot.bin
└── system.img
{{< /highlight >}}

### Flasher toutes les partitions disponible de la rom 
Pour ce faire, vous devez regarder le contenu du print-pit pour associer la bon fichier à la bonne partition.

un exemple de ce que j'ai utilisé pour flashé
{{< highlight terminal >}}
heimdall flash --BOOT boot.img --CACHE cache.img --CM cm.bin --RADIO modem.bin --RECOVERY recovery.img --BOOTLOADER sboot.bin --SYSTEM system.img
{{< /highlight >}}



