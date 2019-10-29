---
title: "Backup reséau TimeMachine avec serveur Samba"
tags: [linux,macos,samba,smb]
date: 2019-10-09T19:34:37-04:00
draft: false
---

Mon garçon utilise un portable Mac pour son apprentissage à l’école. Il passe souvent de très longues heures à travailler sur différent projet à l’aide de celui-ci. Ça serait triste de perdre un travail à cause d’une défaillance informatique ou d’une erreur humaine. J’ai fait plusieurs recherches sur le web pour trouver une façon de faire fonctionner Timemachine sur le réseau via un serveur Linux. Presque tous les articles que j’ai trouvés utilisent le protocole propriétaire de Apple AFP (Apple Filling Protocol).  Le problème est que Apple a l’intention de laisser tomber AFP et de le remplacer par le protocole smb. J’ai trouvé un article sur reddit qui explique comment utiliser les nouvelles options vfs_fruit arrivées dans la version 4.8 de Samba que vous pouvez trouver à ici. https://www.reddit.com/r/homelab/comments/83vkaz/howto_make_time_machine_backups_on_a_samba/


Configuration Samba : /etc/samba/smb.conf

{{< highlight bash >}}


[global]
# Basic Samba configuration
server role = standalone server
passdb backend = tdbsam
obey pam restrictions = yes
security = user
printcap name = /dev/null
load printers = no
socket options = TCP_NODELAY IPTOS_LOWDELAY SO_RCVBUF=524288 SO_SNDBUF=524288
server string = Samba Server %v
map to guest = bad user
dns proxy = no
wide links = yes
follow symlinks = yes
unix extensions = no
acl allow execute always = yes
log file = /var/log/samba/%m.log
max log size = 1000

# Special configuration for Apple's Time Machine
fruit:model = MacPro
fruit:advertise_fullsync = true
fruit:aapl = yes

## Definde your shares here
[TimeMachine Home]
path = /srv/backup/timemachine/%U
valid users = %U
writable = yes
durable handles = yes
kernel oplocks = no
kernel share modes = no
posix locking = no
vfs objects = catia fruit streams_xattr
ea support = yes
browseable = yes
read only = No
inherit acls = yes
fruit:time machine = yes
{{< /highlight >}}


Création d'un usager et du répertoire de sauvegarde

{{< highlight terminal >}}

$ mkdir -p /srv/backup/timemachine/

$ /usr/local/samba/bin/smbpasswd -a $USERNAME

$ mkdir -m 700 /srv/backup/timemachine/$USERNAME; chown $USERNAME /srv/backup/timemachine/$USERNAME
{{< /highlight >}}

À moins d'avoir une partition dédier aux backups il vous sera necessaire d'activer un quota.

Pour ce faire il faut créer le fichier **.com.apple.TimeMachine.quota.plist** avec le contenu suivant:
{{< highlight xml >}}
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>GlobalQuota</key>
    <integer>300000000000</integer>
  </dict>
</plist>
{{< /highlight >}}
300000000000 = 300 Gig


Démarrage et activation des services
{{< highlight terminal >}}
$ sudo systemctl enable smb.service; sudo systemctl start smb.service
{{< /highlight >}}

Pour le premier backup complet, il est intéressant de désactiver le throttle pour utiliser le processeur à pleine capacité. Les deux commandes suivantes se font dans un terminal sur le MacOS utilisant TimeMachine.
{{< highlight terminal >}}
$ sudo sysctl debug.lowpri\_throttle_enabled=0
{{< /highlight >}}

Une fois le backup terminé il faudra revenir à la normal pour les prochains backup incrémentaux.
{{< highlight terminal >}}
$ sudo sysctl debug.lowpri\_throttle_enabled=1
{{< /highlight >}}
